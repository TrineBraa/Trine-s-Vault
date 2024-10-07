[[Building ASP.NET Core Minimal API]]

avhengige injeksjoner i Minimal API
	Routing
	avsløre DTO's istedet for entity objekter
	parameter binding
	forbedre API responsene

Dette kurset bruker Postman og Automapper.


inversion of Control (IoC)
	Inversjon av kontroll
IoC mønster delegerer funksjonen for å velge en konkret implementasjons type for en klasses avhengigheter til en ekstern komponent. 

	Avhengige injeksjoner i Minimal API. Handlerens avhengigheter er gitt av en ekstern komponent
			Ikke ny disse opp.
			
Dependency Injection (DI) Avhengige Injeksjoner
	DI mønsteret bruker et objekt- containeren - til å initialisere andre objekter, håndtere løpet deres og gir det nødvendige avhengighetene til objekter.

DI i minimal API's:

		builder.Services.AddSingleton<TService>(...);
		builder.Service.AddSingleton<T.Service, TImplemantation>(...);
	Singleton er en instans gjennom en request.

		builder.Services.AddScoped<TService>(...);
		builder.Service.AddScoped<TService, TImplementation>(...);
	Scoped betyr at instansen er igjenbrukt i scopet av en request

		builder.Services.AddTransient<TService>(...);
		builder.Service.AddTransient<TService, TImplemantation>(...);
	Transit betyr at en ny instans er gitt når objektet er requested fra containeren.

Hjelpe ekstension metoder eksisterer for normale set av services.

builder.Services.AddDBContext<ObjectDbContext>(o => o.UseSqlite(
builder.Configuration["ConnectionStrings:ObjectDbConnectionString]
));
dbcontext er en entityFramework dependency.

public SomeController(ObjectDbContext objectDbContext)
{
	holder ID i et felt. Dette er en Constructor av en MVC WEB API controller, som varierer utfra DbContext. kontekxten blir innført fra containeren via en constructor injeksjon. 
}

app.MapGet("/Object", (ObjectDbContext, objectDbContext) =>
{
	Implementasjon dette er en injekson via handler parametre. 
})

Routing
Routing in ASP.NET Core | Microsoft Learn:
https://learn.microsoft.com/en-us/aspnet/core/fundamentals/routing?view=aspnetcore-8.0

Prosessen av å matche HTTP metode og URL til en spesifikk route handler. 
Routing er en essensiell del av å bygge en API uansett hvilken du bygger.

aoo.MapAction funksjoner
	-Action er http funksjonen.
	MapGet - Denne leser alle data 
	MapPost - Denne poster en ny data 
	MapDelete - Denne sletter en data
	MapPut - denne setter inn oppdateringer
	
bruk HTTP metoder som de skal, forbedrer realibiliteten til systemet. 
forskjellige komponenter i koden vil ha behov for riktig bruk at HTTP standarden.
		- ikke introduser potensielle uefiktiviteter
		- ikke intdoduser potensielle bugs. 

Route parametre fra URL vil bli bundet til det samme navnet parametret i handler signaturen.

URL:
https:/localhost:7070/Object/randomeGuidID

Route mønster og Handler
app.MapGet("object/{objectID}", () => (Guid objectId) => { ... });


HttpMethodRouteConstraint Class Microsoft.AspNetCore.Routing.Constraints Microsoft Learn:
https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.routing.constraints.httpmethodrouteconstraint?view=aspnetcore-8.0


Route builder methods on IEndpointRouteBuilder
Route templates or patterns
Route Parametres
Route Constraints


Why you shouldn't expose the entity model
	Don't expose the entity model to the outside world.
Entity model
	en måte å representere database rader som objekter (objekt grafer)
	DTO model (data transfer object model)
		en måte å representere data sendt over the wire,
		utover vendt model 

Dish (entity)

public class Dish{
[key]
public Guid Id {get; set;}
public required string Name {get; set;}

public ICollection<ingredient> Ingredients {get; set;} = new List<Ingredient>();
}

Dish(DTO)

public class Dish 
{
public Guid Id {get; set;}
public required string Name {get; set;}
public int IngredientCount {get; set;}
}

Hold entity og DTO modeller separate fører til mer robust, pålitelig utviklende kode.


Parametere binding

Prosessen av konvertering etterspurt data inn i sterkt typet parametre som er utrykket av route handlere.

Det er mange måter å binde parametre [Parameter bindings source]
	- Route Value [FromRoute]
	- Query String [FromQuery]
	- Header [FromHeader]
	- Body (Som JSON) [FromBody]
	- Services provided by DI [FromServices]
	- Custom [ ... ]

Bruke binende atributter er å være uttrykkelig om bindings source.

Modelling Common API Functionality

Filtrering
		- Begrense en samling av resursser, ta hensyn til et predikat
		- filter via query string, bruk name/value kombinasjoner for områder å filtrere på.
Søking
		- søking for passende gjenstander i en samling basert på predefinerte regler.
		- søking via query string, sende en value til å søke etter.
		- Det er opp til API'et å bestemme hva den søker etter og hvordan søket gjennomføres. 
Sortering
		- Sortere via Query string, går gjennom felter og (valgfritt) rettning.
		- typisk uttrykk for dette: orderby=name, desc(ending), price
		- tillat sortering på properties av DTO, ikke på Entity properties. 
Sider
		- Page via query string, sender side nummer og side størrelse
		- page burde være default for preformasjons resultater, du vil ikke laste hele DB samtidig.
		- begrens side størrelse slik at kunder ikke kan få en liste av 10 000 resultater samtidig.
		- returner 'paginator metadata' i en response header slik at kunden vet hvordan de navigerer til neste eller forrige side. 
Ingenting av dette er begrenset til Minimal API.

Status codes and creating responses

Status koder forteller brukeren
	- om requesten wirket som forventet
	- om noe gikk galt, er det en feil av brukeren eller API'et
	- Bruke den rette status koden er essensielt!

vanlige status koder
		- 200 request gikk som forventet
			- 200 - ok 
			- 201 - created
			- 204 - no content (delete)
		- 300 redirection
		- 400 client mistakes (bad request, bruker feil)
			- 401 - Unauthorized
			- 403 - Forbidden
			- 404 - Not found
			- 405 - Method not allowed
		-  500 Internal server error
Minimal API Endpoint return Values
		- String
		- any type of string
		- IResult-based types: 
				- Results.X
				- TypedResults.X


Summary
	 - Injecting dependencies via the handler delegate
	 - routing matches an HTTP method & URI(URL) to a specific route handler
						 - MapGet(...)
						 - MapPost(...)
						 - Stick to the HTTP Standard
	- Design URI's with route patterns
		- Parametres are bound with {  }
	- Constraint matching behavior with route constraints
	- Parametres are bound from a variety of sources
			- Route
			- Query String
			- Headers
			- Request body
	- Do not expose the entity model to improve robustness, reliability and evolvability of your codebase
			- Use DTO instead!
	- Return specific responses via Result . X or TypedResult . X 
		- The consumer relies on the response status code to learn the outcome of a request.