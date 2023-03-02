# assay.it/doc

Web-Site https://assay.it/doc

```bash
podman build -t assay-it-doc .


podman run -it \
  -v $GOPATH/src/github.com/assay-it/doc:/usr/src/app \
  -p 4000:4000 \
  assay-it-doc \
  bundle exec jekyll serve -H 0.0.0.0 -t
```
