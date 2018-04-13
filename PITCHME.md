# Linting

---

### How the linters were chosen
To choose linters:
- gometalinter was chosen as a base:
	- providing:
		- easy execution;
		- configurability;
	- considered to be a useful tool within the go community.

--- 

- A BASH script (😱) was used to go over each go-based repository and :
	- check out the develop branch;
	- execute the gometalinter over the codebase.

---

- The output was inspected for:
	- overly opintionated errors;
	- things that were not considered to be issues. 

---

- On finding the first of these errors, one of the following was executed:
	- the linter reporting the error was disabled;
	- the message type was excluded from the reports using the `--exclude` feature;
	- the code was changed to fit a standard that did not cause any errors.

---

- repeat
	- repeat
		- repeat

---

### Generated files
Many generated files reported errors.  

- Standardise use of generators
- Only use trusted generators
- Don't lint generated files

---

#### go-bindata
Use `bindata.go` for generated filename

- This is default go-bindata behaviour  
- Allows us to easily ignore bindata files
- Easily human identifiable

---

#### proto
Agree to always use `.pb.go` as the generated file extension.

---

### Test files
Tests are important. 

- We include test files in linting to increase quality of our tests.

---

### Linter overlap
Accept linter overlap.

---

### Enabled Linters

---

- **deadcode**  
Detects unused declarations.
- **errcheck**  
Ensures that returned errors are checked.
- **gocyclo**  
Calculate cyclomatic complexities of functions and methods.

---

- **goimports**  
Ensures code adheres to go formatting standards. 
	- implements `gofmt`;
	- checks `import` block formatting. 
- **golint**  
Ensures that code is in a certain style.  
	- Considered opinionated
	- errors reported were considered valid errors
		- except for one.

---

- **lll** (limit to 120 chars)
A line length linter that ensures line length is less than a configurable value.
- **vet**  
Examines code and reports suspicious constructs.
- **ineffassign**  
Detects assignments to variables that are ineffective.

---

- **staticcheck**  
Identifies code for incorrectness that would not be picked up by a compiler.
- **structcheck**  
Finds unused, unexported fields in structs.
- **unconvert**  
Reports redundant type conversions.

---

- **unused**  
Reports unused variables.
- **varcheck**  
Reports unused global variables and constants.

---

### Excluded linters
Useful to run locally, but didn't make the cut.

---

- **vetshadow**  
Vet shadow identifies variables that may have been unintentionally shadowed.
- **dupl**  
'[a] little duplication is better than a little dependency'.  
Duplication issues should be picked up by authors and reviewers. 

---

- **megacheck**  
Does some quite opinionated checks for bugs. It is useful but shouldn't be used as a hard fail.
- **gofmt**  
💯% redundant because of goimports

---

- **goconst**  
Too opinionated; it can be appropriate to hardcode the same string in multiple places.
- **interfacer**  
Identifies possible interfaces

---

### Excluded Messages

---

- **golint**

"can be annoying to use" - _(golint, 2018)_

- Although golint considers that it may be annoying, it can be considered acceptable.

---

- **lll** _(in test files)_  
Some test files need long lines for test cases

---

# WOOOOOOOH