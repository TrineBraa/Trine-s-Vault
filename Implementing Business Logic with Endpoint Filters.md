[[Handling Exceptions and Logging]]

Filter for minimal API
- Sammenligne minimal API filter til ASP.NETCore MVC filter
- Lære om typiske filter senarioer


Filter til minimal API
- ASP.NET Core MVC filter pipeline
	- en pipeline av filter som kjører etter MVC har valgt ut den handlingen som skal gjennomføres. Disse filtrene tillater at du kjører kode før og etter spesifikke stadier i request prosess pipelinen.
	- MVC Filter Pipeline
		- Authorization filters
		- Resource filters
					- Model binding
		- Action filters
					- Action
		- Exception filters
		- Result filters
		- Result executes
			- Denne returneres gjennom pipelinen
				- Result filter
				- Resource filters 
				Resten av filtrene er ignorert i returneringen. 
- Filter for minimal API
	- Det er ingen samenlignbare filter pipeline for minimal API
		- som et alternativ, endpoint filter er støttet. 
			- Endpoint Filter aktive:
				- kjørende kode før og etter endpoint handler
				- inspekter og modifikasjons parametre er gitt gjennom endpoint handler anrop
				- fanger opp respons oppførselen av en endpoint handler.

Typiske senarioer for Filter
- logging etterspørsler/ respons informasjon
- omgjøring av en etterspørsel
- omgjøring av en respons
- tillater eller ikke en etterspørsel
- validering av en inkommende etterspørsel.

En etterspørsel reiser ned listen av endpoint filter før handeleren kjører og så tilbake igjen i revers gjennom listen.
	- rekkefølgen av filtrene er viktig!
	- filter kan også crashe en pipeline, som kan føre at neste filter eller handleren ikke nåes. 

Gjøre Endpoint filter reusable:
	