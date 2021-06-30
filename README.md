# Introduction
This project is a robot extracting and analyzing KYC (Know-Your-Customer)
relevant information from various online sources. These sources include:
- UN, EU and OFAC sanctions for legal and natural persons
- Europol and Interpol wanted lists for natural persons
- General country risk assessment

The setup should be quite straightforward simply open the project in UIPath
studio and run the `Main.xaml` file. In some cases it might be necessary to
update a few variables which we will look at in the different workflows.

Running the robot can take quite long (especially in Debug-Mode). Times of up to
or over 20 minutes are possible.

# Workflows
The project is divided in several sub workflows to facilitate maintenance and
improve readability. The different workflows are presented in the following
sections.

## Main
Wrapper for all the other workflows. Triggers the initial UI dialog and
consolidates the results into an Excel file.

### Important Variables
- `fileName`: Contains the path of the output file. By default contains the name
of the organization or person and a timestamp and is stored in the current
working directory

## Identify Person
Requests user input for the check of natural persons. Displays the UI and
returns the names, birth date and nationality.

### Important Variables
- `OUT: firstName`: Contains the entered first name
- `OUT: lastName`: Contains the entered last name
- `OUT: nationality`: Contains the entered nationality
- `OUT: birthDate`: Contains the entered birthDate

## Identify Organization
Requests user input for the check of legal persons or organizations. Displays
the UI and reads information about beneficial owners and business partners from
a mocked Excel file.

### Important Variables
- `organizationFile`: Path to the organization file with the mapping of
organizations to BPs and additional information
- `OUT: organizationName`: Contains the organization's name
- `OUT: organizationCommercialRegister`: Contains the number of the trade
register entry
- `OUT: organizationCountry`: Contains the name of the organization country
- `OUT: organizationAddress`: Full address of the organization
- `OUT: organizationRevenue`: Revenue information about the company
- `OUT: businessPartnerFirstName`: First name of the business partner
- `OUT: businessPartnerLastName`: Last name of the business partner
- `OUT: businessPartnerNationality`: Nationality of the business partner

## UN Sanction List
Downloads, deserializes and searches the list of UN sanctions for matches with
the supplied parameters. This workflow is able to search for organization
related as well as for person related sanctions.

### Important Variables
- `sanctionFile`: Path were the UN sanction XML file is stored.  This variable
**must** be adjusted to match the robot's environment.
- `IN: isIndividual`: `True` if a person should be checked otherwise `False` for
organizations
- `IN: country`: Name of the organization's country or person's nationality
- `IN: firstName`: Name of the organization or first name of the person
- `IN: lastName`: Last name of the person (persons only)
- `IN: confidenceLevel`: Similarity percentage for string comparisons
(default: 90)
- `OUT: riskLevel`: Risk level of the person or organization
- `OUT: riskReason`: Reason for the assigned risk level. Contains a reference to
an UN sanction code

## EU Sanction List
Downloads, deserializes and searches the list of EU sanctions for matches with
the supplied parameters. This workflow is able to search for organization
related as well as for person related sanctions.

### Important Variables
- `sanctionFile`: Path were the EU sanction XML file is stored. This variable
**must** be adjusted to match the robot's environment.
- `IN: isIndividual`: `True` if a person should be checked otherwise `False` for
organizations
- `IN: country`: Name of the organization's country or person's nationality
- `IN: firstName`: Name of the organization or first name of the person
- `IN: lastName`: Last name of the person (persons only)
- `IN: confidenceLevel`: Similarity percentage for string comparisons
(default: 90)
- `OUT: riskLevel`: Risk level of the person or organization
- `OUT: riskReason`: Reason for the assigned risk level. Contains a reference to
an EU sanction code and list

## OFAC Sanction List
Checks the online database of OFAC (Office of foreign assets control) sanctions
for matches with the supplied parameters. This workflow is able to search for
organization related as well as for person related sanctions.

### Important Variables
- `IN: isIndividual`: `True` if a person should be checked otherwise `False` for
organizations
- `IN: country`: Name of the organization's country or person's nationality
- `IN: firstName`: Name of the organization or first name of the person
- `IN: lastName`: Last name of the person (persons only)
- `IN: confidenceLevel`: Similarity percentage for string comparisons
(default: 80)
- `OUT: riskLevel`: Risk level of the person or organization
- `OUT: riskReason`: Reason for the assigned risk level. Contains a reference to
an OFAC the corresponding list and rekevant program

## Interpol Wanted List
Checks Interpol's online database for red notices with similar person names.
This workflow only works for persons.

### Important Variables
- `IN: country`: The person's nationality
- `IN: firstName`: First name of the person
- `IN: lastName`: Last name of the person
- `IN: confidenceLevel`: Similarity percentage for string comparisons
(default: 90)
- `OUT: riskLevel`: Risk level of the person
- `OUT: riskReason`: Reason for the assigned risk level. Contains a link to the
possible Interpol entry

## Europol Wanted List
Checks Eurpol's online database for fugitives with similar names. This workflow
only works for persons.

### Important Variables
- `IN: firstName`: First name of the person
- `IN: lastName`: Last name of the person
- `IN: confidenceLevel`: Similarity percentage for string comparisons
(default: 90)
- `OUT: riskLevel`: Risk level of the person
- `OUT: riskReason`: Reason for the assigned risk level. Contains a link to the
possible Europol entry

## Country Risks
Evaluates the risks for a country based on a static Excel list which was taken
from (here)[https://www.eulerhermes.com/content/dam/onemarketing/ehndbx/eulerhermes_com/en_US/documents/other/EH-CountryRiskReport-Apr21.pdf].

### Important Variables
- `countryFile`: Path to the country-risk mapping
- `IN: countryName`: Name of the country
- `OUT: riskLevel`: Risk level of the country
