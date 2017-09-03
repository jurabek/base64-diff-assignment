# Base64 Diffing Service

A .NET Core microservice that diffs Base64 encoded binary data.

## Goals

This is a coding test assignment for [WAES](https://www.wearewaes.com/).

The original description of the assignment can be found at [doc/assignment.md](doc/assignment.md).

## Installation

1. Make sure you have installed [.NET Core](https://www.microsoft.com/net/core) (version >= 2.0.0) for your platform.

2. Clone this repository.

    ```bash
    git clone https://github.com:potz/base64-diff-assignment.git
    ```

3. Run the installation script.

    This will restore NuGet packages, build the project and run the tests.

    **On Windows:**

    ```cmd
    CD base64-diff-assignment
    install.bat
    ```

    **On Linux/Mac:**

    ```bash
    cd base64-diff-assignment
    ./install
    ```

4. Launch the server.

    This starts the server and listens on `http://localhost:5000`.

    **On Windows:**

    ```cmd
    run.bat
    ```

    **On Linux/Mac:**

    ```bash
    ./run
    ```

## Usage

If you point your browser to the API root (`http://localhost:5000/`), you'll get some [usage examples](doc/examples.md).

This application is implemented as a REST service. In order to interact with it you need something like [Postman](https://www.getpostman.com/).

This repository includes a [comprehensive Postman test script](doc/Base64Diff.postman_collection.json). It's also [documented online](https://documenter.getpostman.com/collection/view/2689411-1dc29b94-1594-a129-93f8-acbec7af00e4).

If you have Postman installed and the service is running at your local machine's port 5000 right now, you can just click the button below to run it:

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/9597ce812faf7231e029)

## Assumptions

### Specification

* Diff IDs must be integers
* If a client accesses the `GET /v1/diff/:id` endpoint and the server has never before received the left or right part for that particular ID, the service shall respond with a `404 Not found` http status.
* If a client accesses the `GET /v1/diff/:id` endpoint and the server has received only one of the parts (either only the left or only the right), the service shall respond with a `200 OK` http status, and indicate in the response body that the lengths are different (the server is assuming that the missing part is an empty string).

### Data Format

The assignment states:

> Provide 2 http endpoints that accept JSON base64 encoded binary data on both endpoints

My interpretation is that the endpoints must accept a JSON document with a field (I'll use the field name `data`) whose value contains binary data encoded as a Base64 string.

Example of a valid request:

```http
PUT /v1/diff/1/left HTTP/1.1
Host: localhost
Content-type: application/json
Content-length: 78

{
    "data": "VGhlIHF1aWNrIGJyb3duIGZveCBqdW1wcyBvdmVyIHRoZSBsYXp5IGRvZw=="
}

HTTP/1.1 200 OK
Connection: close
Content-Type: application/json; charset=utf-8

{"success":true}
```

### A note about JSON-Base64

There is a (relatively unknown) standards-based file format named [JSON-Base64](https://jb64.org//) which allows encoding of tabular data like CSVs or spreadsheets. In this format the first row defines headers with column names and data types and the remaining of the file contains the actual data, one record per row. All the data is represented as JSON that is encoded via a slightly modified version of Base64. The format is stream-frendly and in public domain.

For this exercise I am assuming that this is **not** what the assignment's description meant. If we were supporting this format then the data would need to be posted to the endpoints as a raw string instead.
