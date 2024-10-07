[[Manipulating Resources]]

Forskjellige muligheter for strukturering av minimal API:
	- Bruke metoder istedet for inline Handlers.
	- Separere handler metoder out i forskjellige klasser
				- eks: DishesHandlers.cs for å holde handlere som håndterer dishes endpoints
				- eks. IngredientHandlers.cs for å holde handlere som håndterer ingredients endpoints. 
			- Du kan på denne måten også gjøre dem static, som unngår å måtte holde på en instans av klassen.
	- Utvide IEndpointRouteBuilder
	- du kan også kombinere 1, 2 og 3 
	- du kan også bruke andre bibloteker 

- Utvidelse av IEndpointRouterBuilder for å strukturere minimal API
		- Du lager en klasse for å holde handlerne, du kan ta f.eks MapGet metoden og flytte den til den nye klassen.
		- Gjøre metoden om til en funksjon og gi den et nytt navn, eks. GetDishesAsync.
		- Dette kan du gjøre med alle handlerne som håndterer forskjellige ting knyttet til klassen du har laget. 
		- Du vil så lage en mappe med utvidelser hvor du vil lage en klasse: EndpointRouteBuilderExtensions
			- her vil du legge inn en metode som f.eks for å registrere en Dish
				- Public Void RegisterDishesEndpoints(this IEndpointBuilder endpointRouteBuilder)
				{
				var dishesEndpoints = endpointRouteBuilder.MapGroup("/dishes");
				dishesEndpoints.MapGet("", DishesHandlers.GetDishAsync);
				}
			- Så i denne vil du kunne legge inn alle handler som MapPost, MapGet, MapPut, MapDelete og du vil da kunne refereere tilbake til disse i koden din og dermed redusere inline kode.
			- I Program vil du da kunne kalle på app.RegisterDishesEndpoints() i dette tilfelle og det vil gjøre program filen mye ryddigere og lesbar. 