# nist-1500-validation
Data files used for validation of the NIST-1500 election systems standards.

This collection of data files is intended to be used by anyone who develops or maintains software that needs to support 
the NIST SP 1500 series of election systems standards. I envision the files being provided as input to test suites 
to exercise such software and ensure compatibility with the standard.

## Target Standards
- [NIST SP 1500-100 rev 2: Election Results Common Data Format Specification Revision 2.0](https://www.nist.gov/publications/election-results-common-data-format-specification-revision-20)
- [NIST SP 1500-101 rev 1: Election Event Logging Common Data Format Specification Draft Version 1.0](https://pages.nist.gov/ElectionEventLogging/)
- [NIST SP 1500-102 rev 1: Voter Records Interchange Common Data Format Specification Version 1.0](https://www.nist.gov/publications/voter-records-interchange-common-data-format-specification-version-10)
- [NIST SP 1500-103 rev 1: Cast Vote Records Common Data Format Specification Version 1.0](https://www.nist.gov/publications/cast-vote-records-common-data-format-specification-version-10)

## Usage

The NIST standards support data exchange in both XML and JSON formats, so both types of data files are provided here.

The structure of this collection is as follows:

- < name of target standard >
    - *classes*: Test files for complex objects defined in the standard.
    - *simple-types*: Test files for primitives, enums, and other simple types defined in the standard.
    
Each data type, simple or complex, is represented by a single XML file and a single JSON file that each contain the 
validation data for that type. The XML and JSON files are generally intended to hold mirror copies of each test, except 
for tests that are only meaningful in one format (such as empty elements in XML, or validation of `@type` attributes in 
JSON). 

Aside from format-specific tests, if there are any tests missing from one or the other, or differences in content 
(besides the expected XML-vs-JSON structural differences) of any test between the two files, please open an issue and 
report it as a bug.

The structure of each JSON file looks like this:

```text
Root object:
{
  "description": <string description of the test file>,
  "data_type": <name from the standard of the data type to be validated>,
  "tests": <a JSON object whose keys are unique test IDs and whose values are an object whose structure is defined below>
}

Test object:
{
  "description": <string describing what particular feature or aspect is to be validated>,
  "valid": <array of values for this data type that should be accepted under the standard>,
  "invalid": <array of values for this data type that should not be accepted under the standard>
}
```

The structure of each XML file is similar:
```xml
<validation_suite>
  <description><!-- string description of the test file --></description>
  <data_type><!-- name from the standard of the data type to be validated --></data_type>
  <tests> <!-- May contain 1..n <test> elements> -->
    <test name="&lt;a name that uniquely identifies the test within this file&gt;">
      <description><!-- string describing what particular feature or aspect is to be validated --></description>
      <valid> <!-- May contain 1..n <value> elements. Absent if no values defined as valid for this test. -->
        <value><!-- A value for this data type that should be accepted under the standard --></value>
      </valid>
      <invalid> <!-- May contain 1..n <value> elements. Absent if no values defined as invalid for this test. -->
        <value><!-- A value for this data type that should not be accepted under the standard --></value>
      </invalid>
    </test>
  </tests>
</validation_suite>
```

The tests are intended to validate lexical and type-level semantic correctness of serializers and deserializers for 
the data types defined by the standards. They do not include load-testing values such as large chunks of data.
They also do not include values that are trivially of different data types, which can be validated by standard 
type-checking features of modern programming languages and libraries. All test values are also syntactically correct 
JSON or XML values.

Serializers that conform to the standard MUST produce identical values to the simple types found in the 'valid' sets 
given the same underlying data; and MUST produce topologically equivalent values for complex types (i.e., identical 
except for differences in key or element ordering where that order is allowed to be arbitrary). They MUST NOT produce 
identical or topologically equivalent values for any of the values found in the 'invalid' sets given the same 
underlying data.

Deserializers that conform to the standard MUST accept all values in the 'valid' sets, and MUST NOT 
accept any values in the 'invalid' sets.

It is not considered a bug for the same value to appear in more than one test. A bit of duplication is considered 
acceptable, as a tradeoff to having to make sure a value is unique across a series of tests that focus on different 
requirements for the type being tested.

## Contributing

This is an effort open to all who wish to contribute. To do so, clone the repository and decide what to work on.
Create a pull request to contribute additions or changes.

Contributions will be checked against the relevant standards to verify correctness.