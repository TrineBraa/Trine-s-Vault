[[Structuring Your Minimal API]]

Håndtering av unntak i minimal API
Forbedre error responser med problem detaljer 
logging i minimal API.

Håndtering av unntak i minimal API:
	- unntaks håndtering er en veldig vanlig oppgave for alle ASP.NET Core applikasjoner
		- Det er ikke veldig forskjellig for minimal API.
	- Developer exception page middleware 
		- eksponerer stablet spor for uhåndterte unntak
			- nyttig under development
			- skal IKKE brukes utenfor et development enviroment.
		- Er automatisk aktivt når
			- programvare kjører i et development miljø
			- og en app er satt opp med et kall til WebApplication.CreateBuilder
	- Exception handler middleware
		- produserer en error payload uten å eksponere stablet spor (stack traces)
			- nyttig når en ikke jobber i et development miljø
			- håndterer og logger unntak
		- den er ikke aktiv som default 
			- aktiveres med et kall til:
				- app.UseExceptionHandler

Forbedring av error respons med problem detaljer
	- dette får du ved å bruke IProblemDetailsService
	- default implementasjon er inkludert med ASP.NET Core
			Aktivere
				builder.Services.AddProblemDetails();
			Effekter av og aktivere developer exception page middleware vil automatisk generere et problem detalj respons.
			 The Exception handler middeware vil automatisk generere en problem detalj respons.
			 status code page middleware kan konfigureres til å generere problem detaljer for tomme bodies ( e.g. : 400, 404, ... )

Logging
	- logging i  minimal API er ikke forskjellig fra 'vanlig' ASP.NET Core
		- Konfigurer en logger
		- innsett en log hvor det eventuelt trengs. 
	- ILogger <DishDTO> 
	- Denne vil logge til Debug skjermen F12 i nettleseren din eller til Consol output vinduet ditt. 

