P3: Tribbler
============

This repository contains the starter code for project 3 (15-440, Fall 2020).
These instructions assume you have set your `GOPATH` to point to the repository's
root `p3/` directory.

**Note that if you choose to test your implementation on AFS cluster, you need to manually install Go 1.15. For more instruction about setting up AFS, please check the README file in the P0 repo.**

## Starter Code

The starter code for this project is organized roughly as follows:

```
bin/                               Student-compiled binaries

sols/                              Staff-compiled binaries
  darwin_amd64/                    Staff-compiled Mac OS X executables
    crunner                        Staff-compiled TribClient-runner
    trunner                        Staff-compiled TribServer-runner
    lrunner                        Staff-compiled Libstore-runner
    srunner                        Staff-compiled StorageServer-runner

  linux_amd64/                     Staff-compiled Linux executables
    (see above)

src/github.com/cmu440/tribbler/
  tribclient/                      TribClient implementation
  tribserver/                      TODO: implement the TribServer
  libstore/                        TODO: implement the Libstore
  storageserver/                   TODO: implement the StorageServer

  util/                            Util functions
    keyFormatter.go                Format the key posted to storage server

  tests/                           Source code for official tests
    proxycounter/                  Utility package used by the official tests
    tribtest/                      Tests the TribServer
    libtest/                       Tests the Libstore
    storagetest/                   Tests the StorageServer
    stresstest/                    Tests everything
    
  tests_cp/                        Source code for official tests (checkpoint)
    (subset of above)

  rpc/
    tribrpc/                       TribServer RPC helpers/constants
    librpc/                        Libstore RPC helpers/constants
    storagerpc/                    StorageServer RPC helpers/constants

tests_cp/                          Shell scripts to run the test for a checkpoint implementation
tests/                             Shell scripts to run the tests for a final implementation
```

## Instructions

### Compiling your code

To and compile your code, execute one or more of the following commands (the
resulting binaries will be located in the `$GOPATH/bin` directory):

```bash
go install github.com/cmu440/tribbler/runners/srunner
go install github.com/cmu440/tribbler/runners/lrunner
go install github.com/cmu440/tribbler/runners/trunner
go install github.com/cmu440/tribbler/runners/crunner
```

To simply check that your code compiles (i.e. without creating the binaries),
you can use the `go build` subcommand to compile an individual package as shown below:

```bash
# Build/compile the "tribserver" package.
go build path/to/tribserver

# A different way to build/compile the "tribserver" package.
go build github.com/cmu440/tribbler/tribserver
```

##### How to Write Go Code

If at any point you have any trouble with building, installing, or testing your code, the article
titled [How to Write Go Code (with GOPATH)](http://golang.org/doc/gopath_code.html) is a great resource for understanding
how Go workspaces are built and organized. You might also find the documentation for the
[`go` command](http://golang.org/cmd/go/) to be helpful. As always, feel free to post your questions
on Piazza.

### Running your code

To run and test the individual components that make up the Tribbler system, we have provided
four simple programs that aim to simplify the process. The programs are located in the
`p3/src/github.com/cmu440/tribbler/runners/` directory and may be executed from anywhere on your system (after `go install`-ing them as discussed above).
Each program is discussed individually below:

##### The `srunner` program

The `srunner` (`StorageServer`-runner) program creates and runs an instance of your
`StorageServer` implementation. Some example usage is provided below (this assumes you are in the `$GOPATH/bin` directory; note you will have to run the commands in separate terminals or in the background with &):

```bash
# Start a single master storage server on port 9009.
./srunner -port=9009

# Start the master on port 9009 and run two additional slaves.
./srunner -port=9009 -N=3
./srunner -port=9008 -master="localhost:9009"
./srunner -port=9007 -master="localhost:9009"
```

For additional usage instructions, please execute `./srunner -help` or consult the `srunner.go` source code.

##### The `lrunner` program

The `lrunner` (`Libstore`-runner) program creates and runs an instance of your `Libstore`
implementation. It enables you to execute `Libstore` methods from the command line, as shown
in the example below:

```bash
# Create one (or more) storage servers in the background.
./srunner -port=9009 &

# Execute Put("thom", "yorke")
./lrunner -port=9009 p thom yorke
OK

# Execute Get("thom")
./lrunner -port=9009 g thom
yorke

# Execute Get("jonny")
./lrunner -port=9009 g jonny
ERROR: Get operation failed with status KeyNotFound
```

Note that the exact error messages that are output by the `lrunner` program may differ
depending on how you implement your `Libstore`. For additional usage instructions, please
execute `./lrunner -help` or consult the `lrunner.go` source code.

##### The `trunner` program

The `trunner` (`TribServer`-runner) program creates and runs an instance of your
`TribServer` implementation. For usage instructions, please execute `./trunner -help` or consult the
`trunner.go` source code. In order to use this program for your own personal testing,
your `Libstore` implementation must function properly and one or more storage servers
(i.e. `srunner` programs) must be running in the background.

##### The `crunner` program

The `crunner` (`TribClient`-runner) program creates and runs an instance of the
`TribClient` implementation we have provided as part of the starter code.
For usage instructions, please execute `./crunner -help` or consult the
`crunner.go` source code. As with the above programs, you'll need to start one or
more Tribbler servers and storage servers beforehand so that the `TribClient`
will have someone to communicate with.

##### Staff-compiled binaries

Last but not least, we have also provided pre-compiled binaries (i.e. they were compiled against our own 
reference solutions) for each of the programs discussed above.
The binaries are located in the `p3/sols/` directory and have been compiled against both 64-bit Mac OS X
and Linux machines. Similar to the staff-compled binaries we provided in project 1,
we hope these will help you test the individual components of your Tribbler system.

### Executing the official tests

#### 1. Checkpoint
The tests for the checkpoint are provided as bash shell scripts in the `p3/tests_cp` directory.
The scripts may be run from anywhere on your system (assuming your `GOPATH` has been set and
they are being executed on a 64-bit Mac OS X or Linux machine). For example, to run the
`libtest.sh` test, simply execute the following:

```bash
$GOPATH/tests_cp/libtest.sh
```

Note that these bash scripts link against both your own implementations as well as the test
code located in the `p3/src/github.com/cmu440/tribbler/tests_cp/` directory. What's more, a few of these tests
will also run against the staff-solution binaries discussed above,
thus enabling us to test the correctness of individual components of your system
as opposed to your entire Tribbler system as a whole.

#### 2. Full test

The tests for the whole project are provided as bash shell scripts in the `p3/tests` directory.
The scripts may be run from anywhere on your system (assuming your `GOPATH` has been set and
they are being executed on a 64-bit Mac OS X or Linux machine). For example, to run the
`libtest.sh` test, simply execute the following:

```bash
$GOPATH/tests/libtest.sh
```

Note that these bash scripts link against both your own implementations as well as the test
code located in the `p3/src/github.com/cmu440/tribbler/tests/` directory. Similarly, a few of these tests
will also run against the staff-solution binaries discussed above,
thus enabling us to test the correctness of individual components of your system
as opposed to your entire Tribbler system as a whole.

If you and your partner are still confused about the behavior of the testing scripts (even
after you've analyzed its source code), please don't hesitate to ask us a question on Piazza!

### Submitting to Gradescope

To submit your code to Gradescope, create a `tribbler.zip` file containing your implementation as follows:

```sh
cd $GOPATH/src/github.com/cmu440
zip -r tribbler.zip tribbler
```

Note: if tribbler.zip already exists and contains files that you have since deleted from your filesystem, the above command will not remove those files from tribbler.zip.  Instead, you will need to delete tribbler.zip and run the command again.  This is a common source of mysterious `go fmt` failures on Gradescope.

## Miscellaneous

### Reading the starter code documentation

Before you begin the project, you should read and understand all of the starter code we provide.
To make this experience a little less traumatic, fire up a web server and read the
documentation in a browser by executing the following command:

```sh
godoc -http=:6060 &
```

If you don't have godoc already, you may have to run:

```sh
go get -v golang.org/x/tools/cmd/godoc
```

Then, navigate to [localhost:6060/pkg/github.com/cmu440/tribbler](http://localhost:6060/pkg/github.com/cmu440/tribbler)
in a browser (note that you can execute this command from anywhere in your system, assuming your `GOPATH`
is set correctly).
