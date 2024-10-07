[[Learning about core concepts and reading Resources]]

Create a resource
		-Generate Links
Updating Resource
Deleting Resource
Dealing with validation

Routing:

HTTP metoder er følgende:
		- Get
		- Post
		- Put
		- Delete
	- Endepunkt rute bygger metode
			- MapGet
			- MapPost
			- MapPut
			- MapDelete

Vært sikker på at URL gir mening
	-Ikke lag en dish via /People
bruk substantiver i URL, ikke Verb
Ikke miks flertall og enkle substantiver.
		- Eks, om du velger å bruke /Dishes bruk også /Ingridients og ikke velg Ingridient.

Retningslinjer for navnsetting er ikke tekniske begrensninger, men de kan holde API'et ditt ryddig og forutsigbart.

Lage en ressurs
	- bruke MapPost for å lage en ny ressurs.
	- Obs på å ikke bruke Entity når du lager en ny ressurs da du ikke vil gjøre den tilgjenngelig til brukere. bruk en DTO.
	- Lag nye klasser for ting de brukes til, ikke bruk dem til andre ting
					- Når du skal lage en ny ressurs, lag en klasse som heter CreateItemDTO.
	- Etter å ha laget ressursen vil du mappe DTO til Entity siden det er Entity som vi ønsker å lagre. 
	- Entity FrameWork Core vil kunne lage en GUID når objektet er lagret som Entity, Du vil kunne mappe dette tilbake til DTO, nå med en ID. Du ønsker ikke at brukeren skal måtte lage en ID til det de legger til i Db.
	- Obs, dersom du tar inn inpouts ra brukeren vil det være sent fra en Body, pass på at dette tas inn som Json slik at systemet kan lese det.

Lage en link til ressurser
	- Du kan hardkode en link til en ressurs, dette er den letteste måten. 
	- Gi et navn til sluttpunktet vil du kunne referere til den når du lager linker!
	- Du bruker linkGenerator. når du ønsker å lage en link, her har du mange forskjelige måter å gjøre det på. 
	- Du lagrer linken i en variabel og sender variabelen i en return. vanligvis via en TypedResults.Created (link, objektet du returnerer)
	- linkGenerator kan settes uansett i koden din.
- Ting å tenke på når du lager en ressurs
		-Når du jobber med foreldre/barn relasjoner må du validere om foreldren eksisterer.
		- Ikke bruk det samme endepunktet for å lage et objekt som for en samling av objekter. Lag et nytt endepunkt istedet: /itemcollections

Oppdatere en ressurs:
	- Lag en ny klasse som gjør at du kan endre objektet du ønsker å finne.  eks, DishForUpdateDTO, Du vil sende inn de feltene du ønsker at brukeren skal kunne endre. 
	- Du vil trenge en route og handeler når du skal gjøre disse endringene.
	- Du sender inn oppdaterings DOT'en samt GUID for å finne den du ønsker å oppdatere. Du vil trenge IMapper for å kunne mappe det fra entity til DTO.  etter endringer vil du Mappe DTO tilbake til entity med de endringene som er gjort. 
	- Du vil også kalle på SaveChangesAsync() for å beholde de endringene i DB.
	Ting og huske på når du oppdaterer ressurser:
			-Sjekk om ressursen eksisterer, inkludert foreldre 
			- Vær forsiktig når du tillater PUT for en samling av ressurser: det kan være veldig destruktiv. 
				- Dersom du oppdaterer 2 objekter i en samling på 100 kan det føre til at etter endringen sitter du igjen med de 2 objektene du oppdaterte. 
			- PUT er for FULLE oppdateringer, PATCH er for del vise oppdateringer.
				- Det som  ikke oppdateres ved bruk av PUT må settes tilbake til sin originale verdi.
	- Change sets for PATCH requests er ofte beskrevet som en liste av operasjoner: en JsonPatchDocument
			- Det er ikke noen støtte for dette for Minimal API.

Slette en ressurs:
	- Lag en handeler og en route, du trenger ikke mapper for å slette en ressurs, ingenting er sendt inn via request body eller returnert i response bodyen i en slett.
	- funksjonen tar imot URL og GUID for å finne det objektet du ønsker å slette. 
	- I forhold til de andre funksjonene vil denne ta Entity formen av objektet og slette den. dette er fordi i denne contexten vil ikke brukeren håndtere Entity enheten noe siden vi kun ønsker å fjerne den fra DB. Husk og lagre med SaveChangesAsync() etter en sletting for å sikre at det du slettes ikke dukker opp igjen når du starter opp DB igjen neste gang. 

Gruppering av ressurser:
	- MapGroup kan brukes for å organisere en samling. 
	- eks dersom du har en samling av Dishes da vil du kunne sette opp en variabel som dette:
				- var dishesEndpoints = app.MapGroup("/Dishes");
						- Når du bruker denne vil du automatisk legge det du lager inn i denne gruppen.
					- Det er best og organisere gruppene som enten funksjonelt eller teknisk tilhører sammen.
				- Du kan gjøre endringer i koden som dette:
					- app.MapGet("/Dishes", ...)
					- dishesEndpoints.MapGet ("", ...)
				- På denne måpten håndteres det av gruppen og du vil kunne minske repetisjonen av kode i samsvar med DRY konseptet.
				- Dersom du også ønsker flere gruppen innen den første gruppen gjør du det på denne måten:
					- var dishWithGuidIdEndpoint = dishesEndpoints.MapGroup("/{dishId)guid}")
				- på denne måten vil denne først gå inn i Dishes før den finner den matchende GUID som du ønsker å fine.
				- Dersom du ønsker å finne en Dish med bruk av Guid vil det fungere på samme måte som den tidligere hvor du legger inn gruppen og fjerner URL samlingen i koden din. 

Forhandling av innhold:
	 - prosessen av å velge den beste representasjonen for en gitt response når det er flere representasjoner tilgjengelig. 
	 - 
		 - eks. GET api/dishes Accept: application/JSON --> API --> { Dishes som JSON}
		- GET api/Dishes Accept: application/xml --> API --> { Dishes som XML }
	- Forhandling av innhold (Content negotiation) er ikke støttet ut av boksen av minimal API (Det er heller ikke planlagt)
	- Dersom ingenting er spesifisert vil den sende og returnere JSON.
	- Dersom du trenger muligheten for å forhandle innholdet. bruk heller ASP.NET Core MVC

Validering i Minimal API
- Input validering er et felles krav
		- felt som kreves, formater, typer og lengde begrensninger, 
		- Kanskje du vil at enkelte felter skal kreves.
		- En felles måte å validere: Annotasjoner,
				public class DishForCreationDto
				{
				[Required]
				[StringLenght ( 100, minimumLenght = 3)]
				public required string Name { get; set; }
				}
		I ASP.NET Core MVC
			- Automatisk validering av innkommende request bodies.
			- Manuel validering med (try)ValidateModel (...).
		Dette er ikke bygget inni Minimal API. Det betyr ikke at du ikke kan bruke det, men du må bygge det fra scratch selv. eventuelt bruk et eksternt biblotek for validering.
			- MiniValidation (Det nærmeste ASP.NET Core sin validerings støtte)
			- FluentValidation (En av de mest populære, støtter minimal API)
			- o9d-aspnet (dette er en gruppe utvidelser for ASP.NET Core, inkluderer validering.)
		- Bruk av dette vil føre til mye repetisjon av kode da validering spesifiseres i hver handler. 
		- Det kommer mer om dette i [[Implementing Business Logic with Endpoint Filters]]

