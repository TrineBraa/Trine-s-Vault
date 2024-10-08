[[Implementing Business Logic with Endpoint Filters]]

Hight-level API security Overview
Token-based security
	-Spesific to minimal API
	- Options for token generation
Authorization and authorization policies


High-level API security Overview
- det er ikke en størrelse passer alle tilnærming når det kommet til sikkerhet knyttet til klienter og APIer

- Appkikasjons level og/eller infrastruktur level?
- På det samme domenet eller cross domener?
- lokal eller sentraliserte brukere og legitimasjon?
- Autentikasjon og/eller autorisasjon?

Token basert sikkerhet er den mest vanlige nå, alt som vil ha tilgang til API'et må gi en tolken som gir tillgang.

Component som genererer og gir tokens  <--> Component som etterspør token --> Component som trenger og validerer token.
	Komponentetn som trenger token vil også kommunisere med generatoren for å sjekke om token er validert. 

Eksempel:
		Token er ofte en JWT (trenger ikke være det heller) 
					- JWT = JSON Web Token
			- En slik token vil inneholde navn og rolle, så du kan sette en bruker til å være f.eks admin
			- AUD = Audience value (API navnet) Dette vil gjøre at API'et kan verifisere at dette token er beregnet til API'et
			- exp er tiden token utgår i Unix tid.

API'et validerer tokenet
	-Den genererer ikke token
	-den gir ikke ut tolken

Token-basert sikkerhet for minimal API
	Autentikasjon er prosessen for å beslutte om noen eller noe er den de sier de er. 
	Dette er det første skrittet i sikkerheten til API'et.
	- Et Token inneholder informasjon som kan verifisere info om hvem (en bruker) eller hva (et system) som ønsker tilgang. 
	- delegasjon er token som tillater tilgang på vegne av bruker eller applikasjon.
- Ingen av disse stegene er en del av API'et og tillhører en autentikasjons service. 
	- Authentication Middleware håndterer dette for ASP.NET Core.
	- for å legge til og konfigurere denne servicen kaller du på:
			- builder.Service.AddAuthentication( );
	- Bind autentikasjonen til JWT bærer autentikasjons håndtereren.
			- builder.Services.AddAuthentication( ).AddJwtBearer( );
			- for JWT bærer må du innstalere en Nugat pakke:
					- Microsoft.AspNetCore.Authentication.JWTBearer

Autorisasjon
 - Prosessen av å bestemme hva noen eller noe har tillatelse til å gjøre. 
	 - For å legge tild ette vil du sette et kall til:
			 - builder.Services.AddAuthorization( );
	- kreve autorisasjon for en bestemt (gruppe av) endepunkt.
			- var ingridientsEndpoints = endpointRouteBuilder
				.MapGroup("/Dishes/{dishID: guid}/ingredients")
				.RequireAuthorization();

![[Pasted image 20241008123556.png]]Dette er hvordan du setter opp innstillingene for JWT bearer for å sikre autentikasjon i projektet

For og sette opp endepunktene dine til å kreve autorisasjon må du sette .RequireAuthorization() inne i de forskjellige endepunktene du har satt opp, eks. MapGet ol. 
Dersom du har en funksjon du ikke trenger autorisasjon på vil du heller sette .AllowAnonymus()


Generere et Token
	Manuelt generere et token på level med API'et. 
			- /login endpoint
			- bra for enkel bruk situasjoner, ikke en god måte for de fleste senarioer. 
	OAuth2 og OpenID Connect er dstandariserte protokoller for token basert sikkerhet. 
			- Token basert sikkerhet på steroider
	Sentraliserte identitets utgivere implementerer disse og genererer tokens
			- Azure AD
			- IdentityServer
			- AuthO
	(ASP).NET Core innkluderer en innbygget verktøy for å generere tokens som kan bli brukt under utvikling.
			- dotnet-user-jwts
jwt.io

![[Pasted image 20241008131113.png]]![[Pasted image 20241008131140.png]]
![[Pasted image 20241008131321.png]]