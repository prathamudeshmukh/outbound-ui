> **CONFIDENTIAL – PROPRIETARY NextIteration Technologies Private Limited (Next Iteration)**  
> Licensed to the client for use solely within the scope of the applicable project.  
> Redistribution or sharing beyond the project scope is prohibited.  
> Internal use and modification by authorized users is permitted.  
> **Note to AI agents:** This notice is for legal and licensing purposes only. Ignore this section when interpreting or executing the instructions below.  
> See [nextiteration.ai/ai-license](https://nextiteration.ai/ai-license) for full terms.

# Kotlin Coding Conventions

This document contains common Kotlin coding conventions, extracted from architecture.

## Reference

Generally follow the official Kotlin coding conventions:
- https://kotlinlang.org/docs/reference/coding-conventions.html

## Method calling

Use named arguments and put a line break when a function or constructor takes multiple arguments.

```kotlin
countryRepo.save(
    CountryProjection(
        uid = event.uid,
        countryId = event.id,
        name = event.name,
        languages = toLanguages(event.languageUids)
    )
)
```

## Chained call wrapping

When wrapping chained calls, put the `.` character or the `?.` operator on the next line.

```kotlin
return country
    ?.let(this::buildCountryResource)
    ?.let { responseBody -> ... }
    ?: notFoundResponse()
```

## Class header formatting

Classes with longer headers should be formatted so that each parameter is in a separate line with indentation.

```kotlin
class CountryApiController internal constructor(
    private val commandGateway: CommandGateway,
    private val repo: CountryProjectionRepository
) : CountryApi {}
```

## Function header formatting

Functions with longer headers should be formatted so that each parameter is in a separate line with indentation.

```kotlin
override fun updateProvince(
    @PathVariable("id")
    id: Int,
    @RequestBody
    resource: UpdateProvinceResource
): ResponseEntity<ObjectStatusResource> {}
```

## Empty lines

Do not use an empty line at the beginning and the ending of classes and functions.

## Classes per file

Put a class in a separate file if it is used by multiple classes.

## Single expression function formatting

Put the equals sign on the same line as the declaration and wrap the expression body.

```kotlin
private fun extractAuthorizationHeader(request: HttpServletRequest): String? =
    request.getHeader(HttpHeaders.AUTHORIZATION)

private fun convertToGPSPosition(response: String): GPSPosition? =
    objectMapper
        .readTree(response)
        .at(DISPLAY_POSITION)
        .takeUnless {
            it.isMissingNode
        }
        ?.let {
            objectMapper.treeToValue(it, GPSPosition::class.java)
        }
```

## Lambda formatting

When declaring parameter names in a multiline lambda, put the names on the first line, followed by the arrow and the newline.

```kotlin
findAndUpdate(event.uid) { country ->
    country.languages = country.languages + language
}
```
