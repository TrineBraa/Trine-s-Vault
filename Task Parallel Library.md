[[Async & Await]]

Creating an Async operation using Task


TPL = Forkortelsen

Bruk av Task uten async og await:
	Du får resultater
	fanger unntak
	kjører fortsettelser utfra suksess eller feiling.
	Avslutte en async operasjon.

utfra fil størrelse og hvor raskt harddisk systemet er, kan lastingen av filen ta lengre tid.

Using the Task:

var response = await client.GetAsync(URL);

resultat av op, Venter på Task, returnerer en Task.

Task representerer en enkel async operasjon.

funksjonalitet gitt ved bruk av Task:
	Utfører arbeid på en forskjellig tråd. ( låser ikke opp nettsiden )
	Få resultatet fra async operasjonen.
	subscribe når operasjonen er gjort ved å introdusere kontinuasjon.
	den kan også gi beskjed om det var et unntak.


Task.Run(() => { Tung operasjon}); // sett denne anonyme metoden I kø I tråd poolet for utførelse.
Task.Run(SomeMetodMetod); // Sett denne metoden i kø i tråd poolet for utførelse.

Dette starter arbeidet på en annen tråd, henter en tråd fra et tråd pool. frier opp UI tråden slik at ikke nettsiden fryser opp å bruker fortsatt kan bruke den selv om tungt arbeid foregår på en annen tråd. 


Generisk og ikke-generisk Task.Run:

Task<T> task = Task.Run<T>(() => { return new T() ; }); //en async operasjon som returnerer en value.

Task task = Task.Run (() => {} );
-----------------------------------------------------------------
En Task kan veldig fort force blokkere UI, så vær veldig forsiktig når du pakker inn async code og vær sikker på at den ikke inneholder blokkerings kode.
unngå å legge tungt arbeid i kø på UI (frontend) tråden.

Task kan lage en fortsettelse uten async og await.

introdusere en fortsettelse:
 var task = Task.Run(() => {} );

 car continuationTask = task.ContinueWith((theTaskThatCompleted) =>{ 
		Koden her er fortsettelsen som vil kjøre etter at Task er ferdig. // Denne fortsettelsen vil gjennomføres på en annen tråd. så den blir 											async
		});

bruk av Result property på en Task er ikke et problem så lenge den har fulført sin async operasjon. Dersom den ikke er ferdig vil Result låse opp koden frem til resultatet er klart. 

Async og Await er mye mer lesbart og lettere og opprettholde. det kan også være unødvendig i noen situasjoner.
Dersom du velger å ikke bruke async og await må du bruke Dispatch for å utføre noe i UI.


Async anonyme metoder:

//Tråd 1
	Task.Run(async () => {
		//Tråd 2

		await Task.Run (() => {
			//Tråd 3
		};
		//Tråd 2

	};
//Tråd 1


Async Anonyme metoder er IKKE det samme som async void.

Håndtering av suksess og feil i Task

ContinueWith vil kjøres når Task er fulført, spiller ingen rolle om det er en suksess, feil eller at den blir kansellert.
du kan legge på et option til ContinueWith funksjonen.
TaskContinuationOptions.{
		OnlyOnFaulted = Kjøres dersom Task har feil
		OnlyOnRanToCompletion = Dersom Task ikke har et unntak eller ikke ble kansellert.
		OnlyOnCanceled = kjøres dersom Task ble kansellert.
		};


Fortsette etter et unntak:

var loadLinesTask = Task.Run(() => { throw new FileNotFoundException(); });
loadLinesTask.ContinueWith((completedTask) => { Denne vil alltid kjøre });
loadLinesTask.ContinueWith ((completedTask) => { Denne vil ikke kjøre om completedTask feilet }, TaskContinuationOptions.OnlyOnRanToCompletion);


Husk å alltid validere Async operasjonene dine!

