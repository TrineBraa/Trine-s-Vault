
Asyncronous Programming in C#

async & Await:

Async er en notasjon om at denne funksjonen skal kjøres async (samtidig) med resten av koden.
men å legge til async nøkkelordet gjør ikke koden automatisk async.
for å faktisk gjøre funksjonen async må du ha en await inni funksjonen. 

Parallel programmering er en måte å dele koden opp i mindre biter om alle løses individuelt. mens den tar I bruk så mye CPU styrke som mulig.
Asynkrone operasjoner jobber parallelt er forskjellen at I Async subscriber du til når operasjonen er fulført.

en Task er en representasjon av en async operasjon.

await nøkkelordet pauser funksjonen til resultatet er tilgjengelig og ungår dermed å blokkere UI kall.
Advarsel: Å bruke nøkkelord som Result og Wait() vil de potensielt blokkere og deadlock applikasjonen dersom det tar lang tid å få informasjonen som når du har en stor DB.

bruk await med en Task for å hente resultatet og for å validere at den fulførte successful.

Bruke Async og await I ASP.NET betyr at Web serveren kan håndtere andre forespørsler.
Await nøkkelordet introduserer en fortsettelse, som tillater deg å komme tilbake til din originale context.
	Koden etter await vil kjøre etter at await er ferdig. Det gjør at du kan skrive en await som pauser fukjsonen til den har fått hentet inn informasjonen før funksjonen fortsetter.

Warning: async void is Evil! Always avoid using async void!
async void brukes KUNN for event handlers.
unntak som skjer i en async void funksjon kan ikke bli fanget i en annen funksjon fordi void ikke returnerer noe!
minimer koden i en async void og vært sikker på at koden alltid er puttet inn I en try/catch.

OBS: 
Task representerer en async operasjon uten en return value!
Task<T> representerer en async operasjon med en return value!

funksjoner merket som async Task vil automatisk ha en Task returnert uten å egentlig måtte returnere noe.

Task returnert fra en async funksjon er en referanse til operasjonen, resultatet eller potensielle error.
brukt alltid await et sted i kjedet av kode for å få async operasjonene.

dersom det ikke er et await nøkkelord, betyr at det ikke lengre er en fortsettelse som vil kjøre og operasjonen er ikke validert. Koden etter hvor en ville satt en await blir 'svelget' av tasken som kjører. 


async Task Good()
{
	throw new Exception("Find me on the Task!");
};

async void Bad()
{
	throw new Exception("No one can catch me!);
};
