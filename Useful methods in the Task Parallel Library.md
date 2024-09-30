[[Task Parallel Library]]


Hvordan vite at alle Tasks i en samling har blitt fulført.
Hvordan kjøre en fortsettelse når hvertfall en task i en saming er fulført.
Starte flere Tasks og prosessere resultatet som det kommer.
Lage en task med 'precomputed' resultat
Lære mer om håndterings context og kontrollere fortsettelser.

- Laste data parallelt ved å utføre flere async operasjoner samtidig.
	Dersom du bruker await nøkkel ordet her vil det føre til at data lastes en etter en.
		istedet fanger vi tasks


	Task.WhenAll

		var task1 Task.Run(() => {return "1";});
		var task2 Task.Run(() => {return "2";});

		var tasks = new [] { task1, task2 };

		string [] result = await Task.WhenAll(tasks);
		
Dette vil lage en ny task som merkes som fulført KUNN når alle tasks i listen er fulført. 

await her vil sikkre at dersom noen av taskene i WhenAll og WhenAny ikke fulfører eller feiler vil unntaket  bli sendt tilbake til contexten.

Du kan så lage en liste av resultatene fra alle Tasks

var allResult = await Task.WhenAll(LoadingTasks);
		WhenAll hindrer en fortsettelse i funksjonen frem til alle taskene i listen er fullført.
allResult.SelectMany (x => x) 
	Denne tar alle listene og putter dem inn i en stor liste.

WhenAll og WhenAny kan ha await og returnerer en task som kan inneholde resultater. 

Ved å bruke "cancellationTokenSource.Cancel ();" Vil du kunne avbryte alle tasks som inneholder cancellationToken.


- Precomputed Results of a task
(For definert resultat)

Du kan lage en 'mock' klass for f.eks en API på denne måten trenger du ikke ha API kjørende hvergang du tester eller jobber med koden.

Du kan hard kode noen resultater inn i mock funksjonen.

Det er unødvendig å markere denne metoden som async siden den ikke kommer til å kjøre som en async funksjon.

Task.CompetedTask

	public override Task Run()
	{
		return Task.CompletedTask;
	}

	await Run():  Er umiddelbart fullført.

Å legge til async og await når du ikke trenger det intorduserer unødvendige komplikasjoner.

Task.FromResult

public Task<IEnumerable<StockPrice>> Get (...)
{
	var stocks = new List <StockPrice>
	{
		new StockPrice {...},
		new StockPrice {...},
		...
	};
	var task = Task.FromResult(stocks);
	return task;
}


- Process Tasks as they Complete

ConcurrentBag er en tråd trygg samling.
List <T> er ikke en tråd trygg samling.

ConcurrenBag lar deg samle data fra mange forskjellige tråder uten fare for å miste data.

- Execution Context and Controlling the continuation

	ConfigureAwait
		var task = Task.Run(() => {...} );
		await task.ConfigureAwait(false);
	ConfigureAwait lar oss la await vite om vi vil gå tilbake til det fangede contexten.

konfigurere hvordan fortsettelsen skal utføres: fortsette på kontexten fanget på UI Tråden?

ConfigureAwait vil kunn konfigurere awaiteren i den metoden den er brukt i. 

ConfigureAwait(false) kan bedre preformanse litt siden den ikke trenger å bytte kontext. Den trenger ikke vente på at den originale kontexten er tilgjengelig.


Don't rely on the Captured Context
	 var task = Task.Run(() =>  { ... });
	 await task.ConfigureAwait(false);
				 Koden under burde ikke trenge den originale kontexten. 

tråd statiske variabler fra den originale kontexten vil ikke være tilgjengelig. 

ASP.NET Core bruker ikke synkronisert kontext som betyr at den ikke vil fange kontext som den tradiosjonelle ASP.NET.

Det gjør at ConfigureAwait(false) er ubrukelig.


Bruk ConfigureAwait i biblioteker:

public async Task MyLibraryMethod()
{
	var task = . . . ;
	var result = await task.ConfigureAwait(false);

Går ikke tilbake til den originale tråden når den håndterer resultatet.
}

Vær forsiktig når du bruker ConfigureAwait da den ikke fanger resultat slik en kanskje forventer. Du må også kunne garantere at du ikke trenger tilgang til noe som lever på den originale tråden ved fortsettelsen.