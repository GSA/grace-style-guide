# GRACE Style Guide

## Index

1. [Golang](#golang)
    - [Linter](#linter)
    - [Testing](#testing)
    - [Mocking](#mocking)
    - [Assertions](#assertions)
    - [Code Coverage](#code-coverage)
1. [Terraform](#terraform)
1. [References for Terraform Style Guides](#references-for-terraform-style-guides)
1. [AWS Resource Naming](#aws-resource-naming)
1. [Public domain](#public-domain)

## Golang

### Linter

GRACE was using the [`gometalinter`](https://github.com/alecthomas/gometalinter) to enforce community recommended style guidelines on Go code.  However, the `gometalinter` project has been deprecated in favor of [`golangci-lint`](https://github.com/golangci/golangci-lint) and GRACE will be migrating to this linter instead.

### Testing

1. Favor using table driven tests (See [Figure 1](#Figure-1)).
2. Keep tests focused. Each test case should test a single behavior and be named accordingly.
3. Use assertions instead of if statements, where possible (See [Figure 2](#Figure-2)).
4. Use sub-tests to differentiate error messages when using assertions. This will make debugging easier.
5. Test cases should cover both the Happy path and the Unhappy path (See [Happy Path vs Unhappy Path](https://hiptest.com/blog/testing/happy-unhappy-paths-why-you-need-to-test-both/)).

### Mocking

1. Use native go interfaces when feasible (See [Figure 3](#Figure-3)).
2. Use GoMock when go interfaces alone are not sufficient (See [Figure 5](#Figure-5)).
3. Find a balance between [DAMP](https://testing.googleblog.com/2019/12/testing-on-toilet-tests-too-dry-make.html) and [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) mocking patterns to improve readability of individual tests while keeping repeated code highly managable (See [Figure-4](#Figure-4)).

### Assertions

1. Use [stretchr/testify](https://godoc.org/github.com/stretchr/testify/assert) as the assertion framework.

### Code Coverage

Well written code will generally have line, branch and mutation coverage higher than 80%. Prioritize testing code that is most likely to change in the future.

### Figure 1

```Go
// Sum ... returns the sum of two numbers
func Sum(a, b int) int {
	return a + b
}

func TestSum(t *testing.T) {
    tt := map[string]struct {
        A        int
        B        int
        Expected int
    }{
        "1+2=3": {1, 2, 3},
        "2+2=4": {2, 2, 4},
    }
    for name, tc := range tt {
        tc := tc
        t.Run(name, func(t *testing.T) {
            actual := Sum(tc.A, tc.B)
            assert.Equal(t, tc.Expected, actual)
        })
    }
}
```

[[top](#grace-style-guide)] [[back](#general-testing)]

### Figure 2

```Go
// Read ... reads the file at 'path' and returns the contents as a string
func Read(path string) (string, error) {
	byt, err := ioutil.ReadFile(path)
	if err != nil {
		return "", err
	}
	return string(byt), nil
}

func TestRead(t *testing.T) {
	tt := map[string]struct {
		Path     string
		Expected string
		Error    bool
	}{
		"File":    {Path: "localfile.json", Expected: `{"data":"this"}`},
		"FileErr": {Path: "non_existent", Error: true},
	}
	for name, tc := range tt {
		tc := tc
		t.Run(name, func(t *testing.T) {
			actual, err := Read(tc.Path)
			if !tc.Error { // Assert not used for readability
				assert.Nil(t, err)
			}
			assert.Equal(t, tc.Expected, actual)
		})
	}
}
```

[[top](#grace-style-guide)] [[back](#general-testing)]

### Figure 3

#### dog/dog.go
```Go
package dog

...

type Dog struct {
}

func New() *Dog {
	return &Dog{}
}

func (d *Dog) Bark() string {
	return "bark!"
}

type IDog interface {
	Bark() string
}

```

#### main.go
```Go
package main

...

func main() {
	d := dog.New()
	PrintMsg(d)
}
func GetSound(t dog.IDog) string {
	return t.Bark()
}
```

#### main_test.go
```Go
package main

...

type MockDog struct {
	dog.IDog
	bark string
}

func (m *MockDog) Bark() string {
	return m.bark
}

func TestGetSound(t *testing.T) {
	expected := "yip!"
	md := &MockDog{bark: expected}
	actual := GetSound(md)
    assert.Equal(t, expected, actual)
}
```

[[top](#grace-style-guide)] [[back](#general-testing)]

### Figure 4

#### main_test.go

```Go
type MockDog struct {
	dog.IDog
	state interface{}
}

func (m *MockDog) Bark() string {
	// In the event that Bark() is called by many methods
	// within the production code, we should abstract
	// the mocked Bark() behaviour by allowing each
	// test case to implement custom behavior.

	// If multiple test cases use some default behavior
	// it might be wise to create a single function whose
	// behavior is consumed by the mocks
	tester, ok := m.state.(dog.IDog)
	if !ok {
		return ""
	}
	return tester.Bark()
}

type yipTestCase struct {
	dog.IDog
	bark string
}

func (y *yipTestCase) Bark() string {
	return strings.ToLower(y.bark)
}

func TestYip(t *testing.T) {
	s := yipTestCase{bark: "yip!"}
	expected := "yip!"
	md := &MockDog{state: &s}
	actual := sound(md)
	assert.Equal(t, expected, actual)
}

type yapTestCase struct {
	dog.IDog
	bark string
}

func (y *yapTestCase) Bark() string {
	// yapTestCase.Bark() implements different functionality
	// in order to satisfy its particular test case needs
	return strings.ToUpper(y.bark)
}

func TestYap(t *testing.T) {
	s := yapTestCase{bark: "yap!"}
	expected := "YAP!"
	md := &MockDog{state: &s}
	actual := sound(md)
	assert.Equal(t, expected, actual)
}
```

[[top](#grace-style-guide)] [[back](#general-testing)]

### Figure 5

#### dog/mock_dog/mock_dog.go
```Go
// Code generated by MockGen. DO NOT EDIT.
// Source: ..\dog.go

// Package mock_dog is a generated GoMock package.
package mock_dog

import (
	gomock "github.com/golang/mock/gomock"
	reflect "reflect"
)

// MockIDog is a mock of IDog interface
type MockIDog struct {
	ctrl     *gomock.Controller
	recorder *MockIDogMockRecorder
}

// MockIDogMockRecorder is the mock recorder for MockIDog
type MockIDogMockRecorder struct {
	mock *MockIDog
}

// NewMockIDog creates a new mock instance
func NewMockIDog(ctrl *gomock.Controller) *MockIDog {
	mock := &MockIDog{ctrl: ctrl}
	mock.recorder = &MockIDogMockRecorder{mock}
	return mock
}

// EXPECT returns an object that allows the caller to indicate expected use
func (m *MockIDog) EXPECT() *MockIDogMockRecorder {
	return m.recorder
}

// Bark mocks base method
func (m *MockIDog) Bark() string {
	m.ctrl.T.Helper()
	ret := m.ctrl.Call(m, "Bark")
	ret0, _ := ret[0].(string)
	return ret0
}

// Bark indicates an expected call of Bark
func (mr *MockIDogMockRecorder) Bark() *gomock.Call {
	mr.mock.ctrl.T.Helper()
	return mr.mock.ctrl.RecordCallWithMethodType(mr.mock, "Bark", reflect.TypeOf((*MockIDog)(nil).Bark))
}

```

#### main_test.go
```Go
package main

...

func TestSound(t *testing.T) {
	ctrl := gomock.NewController(t)
	defer ctrl.Finish()

	expected := "yip!"
	m := mock_dog.NewMockIDog(ctrl)
	m.EXPECT().Bark().Times(1).Return(expected)

	actual := sound(m)
	assert.Equal(t, expected, actual)
}
```

[[top](#grace-style-guide)] [[back](#general-testing)]

## Terraform

The GRACE development team will use the [_Terraform Best Practices_](https://www.terraform-best-practices.com/) GitBook by [Anton Babenko](https://github.com/antonbabenko) as a style guide for GRACE Terraform code.  The team will document additional clarification, modifications, deviations and addendums to the guide in this repository.

[[top](#grace-style-guide)]

### References for Terraform Style Guides

1. [Terraform Best Practices](https://www.terraform-best-practices.com/)
1. [Hashicorp Terraform Style Conventions](https://www.terraform.io/docs/configuration/style.html)
1. [Hashicorp Terraform Recommended Practices](https://www.terraform.io/docs/enterprise/guides/recommended-practices/index.html)
1. [Hashicorp Terraform Getting Started - AWS Dependencies](https://learn.hashicorp.com/terraform/getting-started/dependencies)
1. [GRACE IAM Naming Standard-Draft](https://docs.google.com/document/d/1XWOmYadQg4ya4saVc3IeupfObGmmD6Bqb0nV702ZwGA)
1. [Jon Brouse Terraform Style Guide](https://github.com/jonbrouse/terraform-style-guide)
1. [ASICS Digital Group Terraform Style Guide](https://github.com/asicsdigital/terraform-reference/wiki/Terraform-Style-Guide)
1. [How to use Terraform as a team – Gruntwork - Gruntwork Blog](https://blog.gruntwork.io/how-to-use-terraform-as-a-team-251bc1104973)
1. [Introducing Terraform at GumGum](https://techblog.gumgum.com/articles/introducing-terraform-at-gumgum)
1. [Template/Module iteration terraform style - Google Groups](https://groups.google.com/d/topic/terraform-tool/b97OFyNcNkQ)
1. [Pete's Terraform Tips – Pete Shima – Medium](https://medium.com/@petey5000/petes-terraform-tips-694a3c4c5169)

[[top](#grace-style-guide)]

## AWS Resource Naming

### IAM

There is a draft [_GRACE IAM Naming Standard_](https://docs.google.com/document/d/1XWOmYadQg4ya4saVc3IeupfObGmmD6Bqb0nV702ZwGA) in Google Docs.

## Public domain

This project is in the worldwide [public domain](LICENSE.md). As stated in [CONTRIBUTING](CONTRIBUTING.md):

> This project is in the public domain within the United States, and copyright and related rights in the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](https://creativecommons.org/publicdomain/zero/1.0/).
>
> All contributions to this project will be released under the CC0 dedication. By submitting a pull request, you are agreeing to comply with this waiver of copyright interest.

[[top](#grace-style-guide)]