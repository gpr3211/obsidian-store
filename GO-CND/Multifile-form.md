```go
func (cfg *apiConfig) handlerUploadThumbnail(w http.ResponseWriter, r *http.Request) {
    // validate the request

	const maxMemory = 10 << 20
	r.ParseMultipartForm(maxMemory)

	// "thumbnail" should match the HTML form input name
	file, header, err := r.FormFile("thumbnail")
	if err != nil {
		respondWithError(w, http.StatusBadRequest, "Unable to parse form file", err)
		return
	}
	defer file.Close()

	// `file` is an `io.Reader` that we can read from to get the image data
```

