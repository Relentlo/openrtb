# openrtb [![Go Reference](https://pkg.go.dev/badge/github.com/Relentlo/openrtb.svg)](https://pkg.go.dev/github.com/Relentlo/openrtb) [![Test](https://github.com/mxmCherry/openrtb/actions/workflows/test.yml/badge.svg)](https://github.com/mxmCherry/openrtb/actions/workflows/test.yml)

[OpenRTB](https://iabtechlab.com/standards/openrtb/), [AdCOM](https://iabtechlab.com/standards/openmedia) and [OpenRTB Dynamic Native Ads](https://iabtechlab.com/standards/openrtb-native/) types for [Go programming language](https://golang.org/)

- [openrtb2](openrtb2/) - [OpenRTB](https://iabtechlab.com/standards/openrtb/) [2.5](https://iabtechlab.com/wp-content/uploads/2016/07/OpenRTB-API-Specification-Version-2-5-FINAL.pdf)
- [openrtb3](openrtb3/) - [OpenRTB](https://iabtechlab.com/standards/openrtb/) [3.0](https://github.com/InteractiveAdvertisingBureau/openrtb)
- [adcom1](adcom1/) - [AdCOM](https://iabtechlab.com/standards/openmedia/) [1.0](https://github.com/InteractiveAdvertisingBureau/AdCOM)
- [native1](native1/) - [OpenRTB Dynamic Native Ads API](https://iabtechlab.com/standards/openrtb-native/) [1.2](https://iabtechlab.com/wp-content/uploads/2016/07/OpenRTB-Native-Ads-Specification-Final-1.2.pdf)

**Requires Go 1.13+**

This library is switched to [Go modules](https://golang.org/ref/mod) ([tl;dr](https://blog.golang.org/using-go-modules)) as of [v14.0.0](https://github.com/mxmCherry/openrtb/releases/tag/v14.0.0), so it requires Go [1.11](https://golang.org/doc/go1.11)+ (older Go versions are not capable of using versioned paths).

Also, [test/matcher library](https://github.com/onsi/gomega) relies on newer Go [error handling approach](https://blog.golang.org/go1.13-errors), so tests require Go [1.13](https://golang.org/doc/go1.13)+.

# Using

```bash
go get -u "github.com/Relentlo/openrtb/..."
```

```go
import (
	openrtb2 "github.com/Relentlo/openrtb/openrtb2"

	openrtb3 "github.com/Relentlo/openrtb/openrtb3"
	adcom1 "github.com/Relentlo/openrtb/adcom1"

	native1 "github.com/Relentlo/openrtb/native1"
	nreq "github.com/Relentlo/openrtb/native1/request"
	nres "github.com/Relentlo/openrtb/native1/response"
)
```

This repo follows [semver](http://semver.org/) - see [releases](https://github.com/mxmCherry/openrtb/releases).
Master always contains latest code, so better use some package manager to vendor specific version.

# Guidelines

## Naming convention

- [UpperCamelCase](http://en.wikipedia.org/wiki/CamelCase)
- Capitalized abbreviations (e.g., `AT`, `COPPA`, `PMP` etc.)
- Capitalized `ID` keys

## Types

- Key types should be chosen according to OpenRTB specification (attribute types)
- Numeric types:
  - `int8` - short enums (with values <= 127), boolean-like attributes (like `BidRequest.test`)
  - `int64` - other integral types
  - `float64` - coordinates, prices etc.
- Enums:
  - all enums, described in section 5, must be typed with section name singularized (e.g., "5.2 Banner Ad Types" -> `type BannerAdType int8`)
  - all typed enums must have constants for each element, prefixed with type name (e.g., "5.2 Banner Ad Types - XHTML Text Ad (usually mobile)" -> `const BannerAdTypeXHTMLTextAd BannerAdType = 1`)
  - never use `iota` for enum constants
  - OpenRTB (2.x) section "5.1 Content Categories" should remain untyped and have no constants

## Pointers/omitempty

| Pointer | Omitempty | When to use                                                          | Example                           |
| ------- | --------- | -------------------------------------------------------------------- | --------------------------------- |
| no      | no        | _required_ in spec                                                   | `Audio.mimes`                     |
| yes     | yes       | _required_ in spec, but is a part of mutually-exclusive group        | `Imp.{banner,video,audio,native}` |
| no      | yes       | zero value (`""`, `0`) is useless / has no meaning                   | `Device.ua`                       |
| yes     | yes       | zero value (`""`, `0`) or value absence (`null`) has special meaning | `Device.{dnt,lmt}`                |

Using both pointer and `omitempty` is mostly just to save traffic / generate more "canonical" (strict) JSON.

## Documentation ([pkg.go.dev](https://pkg.go.dev/github.com/Relentlo/openrtb))

- [Godoc: documenting Go code](http://blog.golang.org/godoc-documenting-go-code)
- Each entity (type, struct key or constant) should be documented
- Comments for entities should be copy-pasted "as-is" from OpenRTB specification (except section 5 - replace "table" with "list" there; ideally, each sentence must be on a new line)

## Code organization

- Each RTB type should be kept in its own file, named after type
- File names are in underscore_case, e.g., `type BidRequest` should be declared in `bid_request.go`
- [go fmt your code](https://blog.golang.org/go-fmt-your-code)
- [EditorConfig](https://editorconfig.org/) (not required, but useful)
