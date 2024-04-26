# Bedrift-tracker Arbeidsplan:

## Index:

- [Intro](#intro)
  - [Produkt](#produkt)
  - [Del mål](#del-mål)
  - [Endemål](#endemål)
- [Bruk av Github](#git-hub)
  - [Brancher](#brancher)
  - [Code Review](#code-review)
- [Kode Standarer](#kode-standarer)
  - [Error Håndtering](#error-håndtering)
- [Front End](#front-end)
  - [Formål](#formål-FRONTEND)
  - [Framework](#framework-FRONTEND)
  - [Komponent Liste](#Komponent-liste)
  - [Håndtering av State](#håndtering-av-state)
  - [Routing](#routing-FRONTEND)
  - [Authorizering](#Authorizering)
- [API](#api)
  - [Formål](#formål-API)
  - [Framework](#framework-API)
  - [Routing](#Routing-API)
  - [Micro Services](#micro-services)
  - [Middleware](#middleware)
- [Database](#database)
  - [Formål](#Formål-DATABASE)
  - [Schema](#schema)
  - [Funksjoner](#funksjoner-DATABASE)

## Intro

<br/>

### Produkt

Fullstack app, som skal kombinere data fra PROFF.NO, og samkjøre dette med data fra VIS. <br/>
mth. Oppfølging av bedrifter som har vært gjennom VIS inkubasjon og/eller andre programmer.
Hva skal produktet gjøre:

1. Kobles opp mot Intern Microsoft Authentication Layer for å kunne ta i bruk eksisterende brukere.

2. Ved suksessfull login, skal brukeren presenteres med en graf som viser Gjennomsnittet av omsetting for alle bedrifter i databasen.

3. Header skal presentere logo + navn og to knappe for funksjonalitet.
   - Generer årsrapport.
     - Denne skal hjelpe med å automatisk fylle ut en økonomisk årsrapport for hver bedrift bruker ønsker.
     - Skal ta inn enten en string av organisasjonsnummer, evt et excel ark fult av organisjasjonsnr. Så levere ut et excel ark bruker kan laste ned.
   - Legg til eller oppdater bedrift i tracker.
     - Her skal bruker kunne legge til nye bedrifter til trackersystemet. Det skal kunne ta i mot et excel ark med bedrifter. Excel arket bør inneholde følgende data:
     ```json
     {
       "målbedrift": "bedriftnavn",
       "orgnummer": "bedriftens organisasjonsnavn",
       "fase": "hvilken fase bedriften er i VIS systemet",
       "år": "Hvilket år dette gjelder"
     }
     ```
     - Dette skal og kunne ta i mot oppdatering av eksisterende bedrifter. Dette burde kunne legges med i samme fil, og så filtrerer backenden om dette er en ny, eller en ooppdatering av eksisterende bedrift.
4. En logg-ut knapp under header.

5. På hoved siden, under graf, bør bruker ha mulighet å filtrere data, og evt exportere datasettet til excel.

6. Siden skal også presentere en oversiktlig tabell, som viser bedrift, gjeldene år, samt hva data som blir vist. <br> Bruker skal kunne trykke på en bedrift i tabellene, og grafen skal oppdateres til å vise gjeldene data for den bedriften.
   <br/>

### Del Mål

1. Lage et wireframe oppsett på hvordan siden skal se ut, og hvordan brukeropplevelsen skal være. <a href="https://excalidraw.com/#json=qp0am50gVeJC4uG-84dPQ,30uxisDeckPDPbJXrDlwWg"> Foreløbig ide</a>
2. Designe oppsett og komponenter i FIGMA, Tenke på bruk og formål. Dette er mer et enterprise verktøy enn en butikk. (Legg til figma link her.)
3. Bestemme bruk av Frontend framework, hva som egner seg best, og hva som er lettest å self-hoste på azure. Next? Ren react? JS + HTML? <br> Vi har som mål å bruke NEXTJS som en fullstack løsning for client.
4. Bestemme oss for database oppsett. Vi har som mål å bruke POSTGRESQL for å lagre brukerdata samt data fra proff og vis.
5. Når det kommer til brukertyper, mener vi mht formål at alle brukere har tilgang til samme funksjonalitet.
   <br/>

### Endemål

Vi ønsker å lage et enkelt verktøy for å samle, presentere, og hjelpe med organisering av data for bedrifter som har vært med i VIS sine systemer. <br>
Et verktøy som kan gjøre det lettere å hente, manipulere og bruke data fra bl.a. PROFF for bl.a. generer årsrapporter.

<br/>

## Bruk av GitHub

Vi jobber primært på VIS INNOVASJON sin github, i BT repo.
<br/>

### Brancher

Vi utvikler og pusher endringer til DEV, og holder av push til main primært når vi har nådd milepæler.
<br/>

### Code Review

Vi bruker primært pull-requests for å oppdatere koden i dev. Det gjør at vi har to øyne på koden som går opp til en hver tid.
<br/>

## Kode Standarer

Vi forholder oss til <a href="https://nextjs.org/docs">NextJS docs</a> for best practise. <br>
Vi bruker primært en jsdoc blokk kommentar for å kommentere funksjoner: <br>

```javascript
/**
 * Funksjon som consol.logger "hello world!"
 */
const helloWorld = () => {
  console.log("Hello World!");
};
```

<br>

Vi bruker korte blokk kommentarer for å forklare enkelt funksjonen til en komponent, hvis det trengs:

```javascript
/**
 * underoverskrift til prosjekt.
 *
 */
const MyComponent = () => {
  return <h2>Hello!</h2>;
};
```

<br>

### Error Håndtering

Errorhåndtering bør håndteres så tidlig som mulig.

Eksempel:

```javascript
/**
 * wrapper function over nodefetch
 * @params{string, requestInit}
 */
const fetchData = async (url: string, options: requestInit) => {
  try {
    const response = await fetch(url, options);
    const result = await response.json();
    return { error: null, result: result };
  } catch (error) {
    saveToLog(error);
    return { error: error, result: null };
  }
};
```

Da har vi en oversiktlig måte å passe states gjort av funksjoner som kan throwe errors videre. slik at både server kan evt. korrektere, eller bruker kan håndtere error.

Bruker input kan håndteres på samme måte, her må vi passe på at vi har valideringsfunksjoner som gjøre det lett og oversitklig for bruker å se hva som blir gjort feil.

```javascript
/**
 * Hjelperfunksjon for å validere brukerinput
 *
 */

const validateInputBeforeUse = (input: string) => {
  if (!input.length < 0 || input.length > 200) {
    const error = new Error(
      "Input is outside of permitted range. Keep it between 1-200 characters."
    );
    return { error: error, result: false };
  } else {
    return { error: null, result: true };
  }
};
```

<br/>

## Front End

<br/>

<h3 id="formål-FRONTEND">Formål</h3>

<br/>

<h3 id="framework-FRONTEND">Framework</h3>

<br/>

### Komponent Liste

| Komponent navn | Komponent form | Komponent Beskrivelse |
| :------------- | :------------- | :-------------------- |
| Komponent      | `<Komponent/>` | Er en komponent       |

<br/>

### Håndtering av state

<br/>

<h3 id="routing-FRONTEND">Routing</h3>

<br/>

### Authorizering

<br/>

## API

<br/>

<h3 id="formål-API">Formål<h3>

<br/>

<h3 id="framework-API">Framework</h3>

<br/>

<h3 id="routing-API">Routing</h3>

<br/>

### Micro Services

<br/>

### Middleware

<br/>

## Database

<br/>

<h3 id="formål-DATABASE">Formål</h3>

<br/>

### Schema

<br/>

<h3 id="funksjoner-DATABASE">Funksjoner</h3>
```
