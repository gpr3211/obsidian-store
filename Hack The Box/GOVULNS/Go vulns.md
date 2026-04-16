Vulnerability #1: GO-2025-4014
    Unbounded allocation when parsing GNU sparse map in archive/tar
  More info: https://pkg.go.dev/vuln/GO-2025-4014
  Standard library
    Found in: archive/tar@go1.23.5
    Fixed in: archive/tar@go1.24.8
    Example traces found:
      #1: main.go:52:22: t.main calls tar.Reader.Next

Vulnerability #2: GO-2025-3750
    Inconsistent handling of O_CREATE|O_EXCL on Unix and Windows in os in
    syscall
  More info: https://pkg.go.dev/vuln/GO-2025-3750
  Standard library
    Found in: os@go1.23.5
    Fixed in: os@go1.23.10
    Platforms: windows
    Example traces found:
      #1: main.go:74:29: t.main calls os.Create
      #2: main.go:66:15: t.main calls os.MkdirAll
      #3: main.go:9:2: t.init calls os.init, which calls os.NewFile
      #4: main.go:41:19: t.main calls os.Open
      #5: main.go:66:15: t.main calls os.MkdirAll, which eventually calls syscall.Open

Your code is affected by 2 vulnerabilities from the Go standard library.
This scan also found 0 vulnerabilities in packages you import and 16
vulnerabilities in modules you require, but your code doesn't appear to call
these vulnerabilities.
```go
func main() {
	
	f, err := os.Open("evil.tar")
	if err != nil {
		panic(err)
	}
	defer f.Close()

	
	tr := tar.NewReader(f)

	for {
		hdr, err := tr.Next()
		if err == io.EOF {
			break 
		}
		if err != nil {
			panic(err)
		}

		target := filepath.Join("output", hdr.Name)

		switch hdr.Typeflag {
		case tar.TypeDir:
			os.MkdirAll(target, 0755)
			fmt.Println("Directory:", target)

		case tar.TypeReg:
			// Create parent dirs.
			os.MkdirAll(filepath.Dir(target), 0755)

		
			outFile, err := os.Create(target)
			if err != nil {
				panic(err)
			}

	
			io.Copy(outFile, tr)
			outFile.Close()
			fmt.Println("File:", target)
		}
	}

	fmt.Println("Extraction complete!")
}

```