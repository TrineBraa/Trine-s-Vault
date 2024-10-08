[[Securing your minimal API]]

a few words on Swagger/ OpenAPI
Generating an OpenAPI spesification & documentation UI
Strategies for improving the generated documentation.

Swagger / OpenAPI
	OpenAPI spesifikasjoner
	- Et programmerings språk-agnostik(???) standard for dokumentasjon av HTTP APIer
	- Swagger har de samme spesifikasjonene som OpenAPI
	- vi har spesifikasjoner, fra disse spesifikasjonene et dokument UI, typisk en web app kan bli generert
	- om vi ikke ønsker å skrive disse spesifikasjonene selv trenger vi en komponent eller verktøy som genererer disse spesifikasjonene ved å se gjennom APIet
			- Swashbuckle.AspNetCore den vanligste for spesifikasjoner og UI
				- Disse er også inkludert i Visual Studio API malene.
				- Swashbuckle går gjennom APIet og genererer en spesifikasjon fra det
				- Swashbuckle UI pakker inn swagger-ui, en dokumentasjons interface.
						- ut av boksen er ikke denne spesifikasjonen den beste
	hjelp for forbedring av OpenAPI spesifikasjoner er gitt via
			-Microsoft.AspNetCore.OpenApi

Ved bruk av OpenAPI og Swagger kan du legge inn summary eller description for å gi brukere mer info hva de forskjellige blockene av kode gjør
			.WithSummary ( en kort linje som beskriver hva denne koden gjør)
			.WithDescription (En lengre texts som gir mer beskrivelse hva koden gjør.)
		For å aktivere disse kallene må du ha en Nugat pakke som tillater slik type interaksjon med Open API
			- Microsoft.AspBetCore.OpenApi
			- Du må også ha et kall for å aktivere denne pakken før du gjør de andre with kallene over.
				.WithOpenApi()
				