# Linting

---

### Requirements
- Linters must not alter any of the codebase, they must only report whether the code meets a certain criteria.
- The developed linting tool should be able to be run easily locally in a development environment, as well as in a CI/CD situation.
	- The tool should be able to be run on an uncommitted, unpushed, work in progress, it should not require that the codebase has a commit hash that could retrieve the codebase from a remote repository.
- The tool should be produce exactly the same results when run on the same codebase, whether run locally or in a CI/CD environment.

---

### How the linters were chosen
To choose linters, the linters of the gometalinter were chosen as a base set, with the use of the gometalinter as a way to execute them because it provides easy configurability and is generally considered to be a useful tool within the go community.
- A BASH script (😱) was used to go over each go-based repository, package or application, within one of our engineers' machines and:
	- check out the develop branch. If there was no develop branch available, the development branch was used;
	- executed the gometalinter over the codebase.
- The output from all of the repositories of the gometalinter was output into a single file for each run.
- The output was inspected for errors that appeared to be _too_ opinionated or were perhaps things that were not considered yet to be issues. On finding the first of these errors, either:
	- the linter reporting the error was disabled;
	- the message type of the reported error was excluded from the reports using the gometalinter's `--exclude` feature;
	- the code was changed to fit a standard that did not cause any errors (see Generated files section).
- The script was then run again, until all errors reported were agreed with.

---

### Generated files
Many files that are auto-generated have lots of things brought up by linters, however, we should only be using generators that we trust the output of when we use them properly. If we can standardise the way we use generators, such as only outputting files called bindata.go for go-bindata, then we will be able to skip them in the checking process.

There is a proposal, and some work done already, in go to identify generated files automatically and this feature will, no doubt, be integrated into many linters eventually. Until this feature becomes stable and available, we will have to use auto-generators in a standardised way; this is a good idea anyway.

---

#### go-bindata
By using the default name of bindata.go for `go-bindata` we will be able to identify these files easily. From looking over our repositories in their current format, there seems to be no reason for us to rename the generated files anyway. Another benefit of using the default name is that we will need one less argument to the bindata command and they will stand out when an author is looking at the codebase.

---

#### proto
If we ensure that we always have .pb as the protofbuf extension and always us .pb.go as the generated file extension then we can ignore these files using when using linters.

For example, where should the generated file go? By default, it goes in the working directory of where the command runs. Maybe this is actually where the generated files should be?

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
