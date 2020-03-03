# Asciidoctor docker action

You can use Asciidoctor command easily if you use this action

## Inputs

### `shellcommand`

**required** 
bash command   
default 'asciidoctor index.adoc'

## Example

### Example Nomal
```
steps:
- name: Build AsciiDoc step
  id: documents
  uses: Analog-inc/asciidoctor-action@master
  with:
    shellcommand: "asciidoctor index.adoc" 
```

### Example shell script

Copy [into.github/workflows] to your repository [.github/workflows]

.github/workflows/asciidoc.sh


```
#!/bin/bash
set -e

mkdir -p ./outputs/pdf
mkdir -p ./outputs/html
mkdir -p ./outputs/ebub

CURRENT_PATH=`pwd`
ASCIIDOCTOR_PDF_DIR=`gem contents asciidoctor-pdf --show-install-dir`

# cp "${ASCIIDOCTOR_PDF_DIR}/data/themes/default-theme.yml" ${CURRENT_PATH}/themes/default-theme.yml
#  cp -r -f "${ASCIIDOCTOR_PDF_DIR}/data/fonts/" ${CURRENT_PATH}/  

# -a, --attribute=ATTRIBUTE
# -B, --base-dir=DIR
# -D, --destination-dir=DIR
# -o, --out-file=OUT_FILE
# -R, --source-dir=DIR
# -b, --backend=BACKEND
# -d, --doctype=DOCTYPE
# -r, --require=LIBRARY

asciidoctor -B ${CURRENT_PATH}/ -D ${CURRENT_PATH}/outputs/html/ -o index.html  -a imagesdir=${CURRENT_PATH}/images  -r asciidoctor-diagram   index.adoc


asciidoctor-pdf -B ${CURRENT_PATH}/ -D ${CURRENT_PATH}/outputs/pdf/ -o index.pdf -a imagesdir=${CURRENT_PATH}/images  -a scripts@=cjk  -a pdf-styledir=${CURRENT_PATH}/themes -a pdf-fontsdir=${CURRENT_PATH}/fonts -r asciidoctor-diagram -r ${CURRENT_PATH}/configs/config.rb  index.adoc


asciidoctor-epub3 -B ${CURRENT_PATH}/ -D ${CURRENT_PATH}/outputs/ebub/ -o index.epub  -r asciidoctor-diagram -a imagesdir=${CURRENT_PATH}/images   index.adoc
```

.github/workflows/main.yml
```
name: CI

on: [push]

jobs:
  asciidoctor_job:
    runs-on: ubuntu-latest
    name: Build AsciiDoctor
    steps:
    - name: Check out code
      uses: actions/checkout@v2
      ## https://github.com/actions/checkout
      # with:
      # Default: ${{ github.repository }}
      # repository: ''
      # Otherwise, defaults to `master`.
      # ref: ''
      # Default: ${{ github.token }}
      # token: ''
      # Default: true
      # persist-credentials: ''
      # Relative path under $GITHUB_WORKSPACE to place the repository
      # path: ''
      # Default: true
      # clean: ''
      # Default: 1
      # fetch-depth: ''
      # Default: false
      # lfs: ''
    - name: Build AsciiDoc step
      id: documents
      uses: Analog-inc/asciidoctor-action@master
      with:
        shellcommand: "./.github/workflows/asciidoc.sh" 
    # Use the output from the documents step
    - name: Save AsciiDoc step
      uses: actions/upload-artifact@v1
      with:
        name: Docs
        path: ./outputs/
```
