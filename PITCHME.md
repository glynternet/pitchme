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
- A single file was output for each script run, containing results for all repositories.

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
Proposal:
- Standardise use of generators
- Only use trusted generators
- Don't lint generated files

---

#### go-bindata
Proposal:
- Use default name of `bindata.go` for generated file

- Not only will this allow us to ignore bindata files, it will also allow us to easily identify, as humans, which files are generated.

---

#### proto
Proposal:
- Agree to always use `.pb.go` as the generated file extension.

---

### Test files
Test files are extremely important. We include test files in our linting and checking to ensure the quality and reliability of our tests.

---

### Linter overlap
Some of the linters may actually give duplicated output, we have decided that this should be fine. If it turns out to be really annoying then we can revert this decision later.

---

### Enabled Linters
- **deadcode**  
Detects unused declarations.
- **errcheck**  
Ensures that returned errors are checked.
- **gocyclo**  
Calculate cyclomatic complexities of functions and methods.
- **goimports**  
Ensures that code adheres to go formatting standards. `goimports` implements `gofmt` and checks that the `import` block is formatted correctly. When `goimports` is ran as part of `gometalinter`, no changes are made to the code.
- **golint**  
Ensures that code is in a certain style. Although `golint` is considered opinionated, the errors that were brought up across our codebase we considered valid errors, apart from the message about returning unexported types, _see below_.
- **lll**  
A line length linter that ensures line length is less than a configurable value.
- **vet**  
Examines code and reports suspicious constructs.
- **ineffassign**  
Detects assignments to variables that are ineffective.
- **staticcheck**  
Identifies code for incorrectness that would not be picked up by a compiler.
- **structcheck**  
Finds unused fields in structs. `structcheck` will not report unused exported fields as these can be in place for use by other packages.
- **unconvert**  
Reports redundant type conversions.
- **unused**  
Reports unused variables.
- **varcheck**  
Reports unused global variables and constants.

---

### Excluded linters
The following is a list of linters that are excluded from our checks. These can be useful in identifying possible issues with our code but we cannot rely on every single message that is output by them, they can be too opinionated.  

It is still recommended to run the following linters locally when developing:
- **vetshadow**  
There is no clear decision around whether we consider shadowing variables to be bad practise or not. Vet shadow identifies variables that may have been unintentionally shadowed. If we want to include this linter at a later date, we can make a decision then.
- **dupl**  
A go proverb states that '[a] little duplication is better than a little dependency'. Design issues around duplication should be picked up by authors and reviewers, rather than depending on this tool. 
- **megacheck**  
Does some quite opinionated checks for bugs. It is useful but shouldn't be used as a hard fail.
- **gofmt**  
Redundant because of goimports
- **goconst**  
It may not always be appropriate to call the result from goconst an error. Sometimes it can be appropriate to hardcode the same string in multiple places.
- **interfacer**  
Should not be forced but it a good one to run localy

---

### Excluded Messages
- **golint**  
When an unexported struct is returned from an exported function or method, `golint` complains that it "can be annoying to use". Although golint considers that it may be annoying, we have chosen that this is acceptable.
- **lll**  
We ignore `lll` results that are in test files. For a lot of our test files, we have lines that are longer than 120 characters long. It is acceptable that for some test cases to have lines longer than this length due to things such as urls that are hardcoded test cases
