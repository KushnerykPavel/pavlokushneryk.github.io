## Golang LOC Counter

---

Hello, today we will discuss solution of Coding Challenge #92 - LOC [(Lines of Code) Counter](https://codingchallenges.substack.com/p/coding-challenge-92-loc-counter).
We will implement a simple Go program that counts the number of lines of code in a given directory, excluding comments and empty lines. During the implementation, we will 
also use some of the best practices in Go programming such as using `filepath.Walk` for directory traversal and `bufio.Scanner` for reading files line by line.

We will get the next result
```
────────────────────────────────────────────────────────────────────────
Language |Lines |Blanks |Comments |Code
────────────────────────────────────────────────────────────────────────
Java       |149 |
Rust       |58  |
JavaScript |105 |
Go         |335 |
PHP        |13  |
Python     |12  |
────────────────────────────────────────────────────────────────────────
Total |746 |52 |18
────────────────────────────────────────────────────────────────────────
```
Solution is available on [GitHub](https://github.com/KushnerykPavel/go-loc)

### Implementation

---

#### File traversal and reading 
According to the task, our program should receive a directory path as an argument and count the lines of code in all files in that directory and its subdirectories.
We will use the `filepath.Walk` function to traverse the directory and its subdirectories. Also, we have added semaphore to limit the number of goroutines that can read files concurrently.

```go
    err := filepath.Walk(dir, func (path string, info os.FileInfo, err error) error {
		if !strings.HasSuffix(path, ext) {
			return nil
		}
		
		if info.IsDir() {
			return nil
		}
		
		if err := sem.Acquire(ctx, 1); err != nil {
			return err
		}
		
		wg.Add(1)
		go func(p string) {
			defer sem.Release(1)
			defer wg.Done()
			fileReader, err := os.Open(path)
			if err != nil {
				slog.Error("Error opening file:", err)
				return
			}
			
			defer fileReader.Close()
			runResult := itrp.Run(fileReader)
			mu.Lock()
			result.EmptyLines += runResult.EmptyLines
			result.TotalLines += runResult.TotalLines
			result.CommentLines += runResult.CommentLines
			
			for lang, count := range runResult.CodeLines {
				result.CodeLines[lang] += count
			}
			
			mu.Unlock()
		}(path)
		
		return nil
})

```

#### Interpreter

Next, we will implement the `Interpreter` interface that will be responsible for reading files and counting lines of code. We will use `bufio.Scanner` to read files line by line and check each line for comments and empty lines.
Interpreter has method `Run` that accepts `io.Reader` that allow us to read file by line and avoid high memory usage for large files.

```go
func (i *Interpreter) Run(reader io.Reader) Result {
	scanner := bufio.NewScanner(reader)
	empty, total, comment := 0, 0, 0
	codeLines := map[detector.LanguageName]int{}
	lastDetectedLanguage := detector.LanguageUnknown

	for scanner.Scan() {
		line := scanner.Text()
		total++

		switch {
		case isEmpty(line):
			empty++
		case i.isOneLineComment(line):
			comment++
		case i.isMultiLineCommentStart(line):
			c := i.consumeMultiLineComment(scanner)
			comment += c
			if !i.isStringStartWithComment(line) {
				comment -= 1
			}
			total += c - 1
		default:
			detectedLanguage := i.detector.Detect(line)
			if detectedLanguage == detector.LanguageUnknown {
				detectedLanguage = lastDetectedLanguage
			}
			lastDetectedLanguage = detectedLanguage
			codeLines[detectedLanguage]++
		}
	}

	return Result{EmptyLines: empty, TotalLines: total, CommentLines: comment, CodeLines: codeLines}
}
```
Method covered by multiple [tests](https://github.com/KushnerykPavel/go-loc/blob/main/internal/interpreter/interpreter_test.go#L11). 

#### Code detector

Code detector realized as regex based euristic list that allows us detect programming language. Example of detector config 
```go 
	{
		Language: LanguageRust,
		Patterns: []Pattern{
			{
				Regex:  *regexp.MustCompile(`\blet\s+mut?\s*\w+`),
				Points: 3,
			},
			{
				Regex:  *regexp.MustCompile(`\bfn\s+\w+\s*\(.*\)\s*(->\s*\w+)?\s*{`),
				Points: 3,
			},
			{
				Regex:  *regexp.MustCompile(`\buse\s+[\w:]+(::\*)?;`),
				Points: 2,
			},
			{
				Regex:  *regexp.MustCompile(`\bmod\s+\w+;`),
				Points: 2,
			},
			{
				Regex:  *regexp.MustCompile(`\bimpl\s+[\w<>,\s]+{`),
				Points: 2,
			},
			{
				Regex:  *regexp.MustCompile(`\benum\s+\w+\s*{`),
				Points: 2,
			},
			{
				Regex:  *regexp.MustCompile(`\bmatch\s+\w+\s*{`),
				Points: 2,
			},
			{
				Regex:  *regexp.MustCompile(`\bSome\(\w+\)|None\b`),
				Points: 2,
			},
			{
				Regex:  *regexp.MustCompile(`\bResult<.+?>`),
				Points: 2,
			},
			{
				Regex:  *regexp.MustCompile(`\bprintln!\s*\(".*"\)`),
				Points: 1,
			},
			{
				Regex:  *regexp.MustCompile(`::\w+`),
				Points: 1,
			},
			{
				Regex:  *regexp.MustCompile(`\bBox::new\(`),
				Points: 1,
			},
		},
	},
```

Points is weight of allowance of code line to be detected as programming language. If line match multiple patterns, we will use pattern with maximum points.

#### Main function

Finally, we will implement the main function that will parse command line arguments, initialize the interpreter and detector, and start the file traversal and reading process.
In order to run the program you need to compile it and run the next command:

```bash
make

./bin/main . .go 
```

The result is:
```
────────────────────────────────────────────────────────────────────────
Language |Lines |Blanks |Comments |Code
────────────────────────────────────────────────────────────────────────
Java       |149 |
Rust       |58  |
JavaScript |105 |
Go         |335 |
PHP        |13  |
Python     |12  |
────────────────────────────────────────────────────────────────────────
Total |746 |52 |18
────────────────────────────────────────────────────────────────────────
```

#### Conclusion
In this article, we have implemented a simple Go program that counts the number of lines of code in a given directory, excluding comments and empty lines. We have used some of the best practices in Go programming such as using `filepath.Walk` for directory traversal and `bufio.Scanner` for reading files line by line. The program is efficient and can handle large files without high memory usage. You can find the complete code on [GitHub](https://github.com/KushnerykPavel/go-loc)

## Thanks for reading!