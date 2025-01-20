# Ruby version determination

Determine the Ruby versions for your testing matrix based on the gemspec.

Maintaining your CI is tedious and your gem can already specify the compatible Ruby versions using [required_ruby_version](https://guides.rubygems.org/specification-reference/#required_ruby_version).
This action reads your gemspec and provides an output of compatible Ruby versions.

## Supported Ruby versions

A static list of compatible versions is maintained.
This is a subset of [setup-ruby's supported versions](https://github.com/ruby/setup-ruby#supported-versions).

* 3.3
* 3.2
* 3.1
* 3.0
* 2.7
* 2.6
* 2.5
* 2.4

## Example

A minimal example has a single job to determine the Ruby version matrix, using this repository.
Then there should be some task that uses the matrix, but the exact steps likely differ.

```yaml
jobs:
  setup_matrix:
    name: "Determine Ruby versions"
    runs-on: ubuntu-latest
    outputs:
      ruby: ${{ steps.ruby.outputs.versions }}
    steps:
      - id: ruby
        uses: ekohl/ruby-version@v0

  test:
    name: "Ruby ${{ matrix.ruby }}"
    runs-on: ubuntu-latest
    needs: setup_matrix
    strategy:
      matrix:
        ruby: ${{ fromJSON(needs.setup_matrix.outputs.ruby) }}
    steps:
      - uses: actions/checkout@v4
      - name: Setup ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: ${{ matrix.ruby }}
          bundler-cache: true
      - name: Run tests
        run: rake test
```
