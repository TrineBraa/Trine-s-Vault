[[Task Parallel Library]]

Ikke tving en bruker til å vente på et resultat når de vet det er feil, La dem stanse oppgaven.

CancellationTokenSource

Signaliserer til en CancellationToken at det skal avbrytes
Alltid kall Dispose() på avbryt token source etter async operasjonen er ferdig.

Cancellation Token Sourse:

CancellationTokenSource cancellationTokenSource;
					.Cancel();
					.CancelAfter (5000); Avslutter etter 5 sekunder
					.Dispose()
		Settes sammen med en Token som kan sendes inn i en async operasjon. 
			Task.Run (() => {}, token); Sendes inn i en funksjon som et tegn på at den er avbrutt.
		Task.Run (() => {
			if (token.IsCancellationRequested) {} Denne sjekker om en avbrytelse er requested. 
		});

Kallet på Cancel vil ikke automatisk avbryte en Async operasjon. 
	Avbrytelsen må håndteres i koden.

Cancellation Token og ContinueWith

CancellationTokenSource cancellationTokenSource;
CancellationToken token = CancellationTokenSource.Token;

var task = Task.Run(() => {}, token );
task.ContinueWith((t) => {}, token);
				Her vil igjen ikke avbrytelsen gjøres automatisk å må hånteres i koden med f.eks. en if statement. 
Du kan også sette opp en ContinueWith til å kunn kjøre ved avbrytelse. 

Obs, dersom du setter CancellationToken til (Canceled: True) vil da markere at som avbrutt og ingenting vil kjøre. 

Dette gjelder spesielt ved bruk av ContinueWith, dersom ting ikke er koblet opp på en skikkelig måte til funksjonen som ble avbrutt vil det komme introdusere en del bugs.  

Hvert bibliotek kan håndtere avbrytelser forskjellig.
	noen vil returnere deler av dataen mens andre vil gi et unntak ved avbrytelser. 


