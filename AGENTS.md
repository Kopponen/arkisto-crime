# Arkisto-crime – kattava Codex-ohjeistus

Tämä dokumentti on tarkoitettu käytettäväksi suoraan uuden repositorion juureen tiedostona `AGENTS.md`. Sen jälkeen voit antaa Codexille ensimmäisen tehtävän tämän dokumentin lopussa olevalla käynnistyspromptilla.

---

## 1. Projektin tavoite

Rakennetaan alusta nimeltä **Arkisto-crime**.

Palvelu löytää, jäsentää ja julkaisee historiallisista arkistolähteistä perusteltuja true crime -tapauksia. Ensimmäinen versio ei ole vain sisältösivusto, vaan tuotantokelpoinen web-sovellus, jossa on:

* julkinen etusivu
* tapauslistaus
* yksittäinen tapaussivu
* lähdeviitteisiin perustuva tietomalli
* editorin hallintapaneeli tapausten syöttämistä varten
* selkeä erotus lähdekatkelman, toimituksellisen yhteenvedon ja julkaistun tarinan välillä
* tekninen perusta myöhempää AI-avusteista sisällöntuotantoa varten

Tarkoitus on rakentaa vankka MVP, jota voidaan jatkaa tuotantoon ilman uudelleenkirjoitusta.

---

## 2. Tuotevisio

Arkisto-crime ei saa näyttää geneeriseltä blogilta. Sen pitää tuntua uskottavalta historiallisten rikostapausten tuotteelta.

Ydinarvo käyttäjälle:

* löytää kiinnostavia historiallisia rikostapauksia
* nähdä mihin lähteisiin tarina perustuu
* lukea toimituksellisesti kirjoitettu, helposti ymmärrettävä versio vaikeasta arkistoaineistosta
* suodattaa tapauksia ajan, alueen ja rikostyypin mukaan

Ydinarvo toimittajalle tai ylläpitäjälle:

* syöttää raakaa lähdeaineistoa hallitusti
* erottaa lähde, tulkinta ja julkaistu teksti toisistaan
* ylläpitää metatietoa ilman suoraa tietokantatyötä

---

## 3. Teknologiapäätökset

Käytä seuraavia oletuksia, ellei käyttäjä erikseen muuta niitä:

* **Framework:** Next.js 15+ App Router
* **Language:** TypeScript
* **UI:** React Server Components oletuksena
* **Styling:** Tailwind CSS
* **Componentit:** shadcn/ui
* **ORM:** Prisma
* **Database devissä:** SQLite
* **Database tuotannossa:** PostgreSQL-yhteensopiva rakenne
* **Auth adminille:** NextAuth tai vaihtoehtoisesti hyvin kevyt placeholder-auth ensimmäisessä vaiheessa
* **Validation:** Zod
* **Forms:** React Hook Form
* **Testing:** Vitest yksikkötesteihin, Playwright perus-e2e-testeihin
* **Linting/formatointi:** ESLint + Prettier
* **Package manager:** pnpm

Jos jokin riippuvuus on ristiriidassa toisen kanssa, suosi mahdollisimman yksinkertaista, hyvin tuettua ratkaisua.

---

## 4. Ensivaiheen ominaisuudet

Codexin pitää rakentaa ensimmäisessä kehityssyklissä nämä kokonaisuudet:

### Julkinen käyttöliittymä

* etusivu
* tapauslista
* tapaussuodattimet:

  * vuosisata
  * alue
  * rikostyyppi
* yksittäinen tapaussivu
* lähteet ja toimituksellinen vastuuvapauslauseke näkyviin tapaussivulla

### Admin-käyttöliittymä

* kirjautumisen paikka tai placeholder-rakenne
* näkymä uuden tapauksen syöttöön
* CRUD ainakin seuraaville:

  * case
  * source reference
  * story draft
* lomake, jossa erotellaan:

  * alkuperäinen lähdekatkelma
  * modernisoitu referaatti
  * julkaistava narratiivinen teksti

### Data ja domain-malli

* `Case`
* `SourceReference`
* `StoryDraft`
* mahdollisesti myös:

  * `Tag`
  * `Location`
  * `PersonMention`
  * `EditorialNote`

### Laatu ja tuotantovalmius

* siisti tietorakenne
* perusvirheenkäsittely
* siemen-/demoaineisto
* testit kriittisille poluille
* README, jonka perusteella uusi kehittäjä saa projektin käyntiin

---

## 5. Domain-mallin periaatteet

Mallinna data niin, että historiallinen aineisto säilyy erotettuna toimituksellisesta tulkinnasta.

### Pakollinen semanttinen erotus

1. **SourceReference** = arkistoon tai lähteeseen liittyvä metadata ja mahdollinen ote
2. **StoryDraft** = toimituksellinen työvaihe, jota ei ole vielä julkaistu
3. **Case** = julkaistava kokonaisuus, jolla on slug, otsikko, ingressi, status ja näkyvä sisältö

### Case-kenttiä vähintään

* id
* slug
* title
* summary
* body
* crimeType
* region
* century
* yearStart
* yearEnd
* status (`draft`, `review`, `published`)
* featured
* createdAt
* updatedAt

### SourceReference-kenttiä vähintään

* id
* caseId
* archiveName
* collectionName
* sourceTitle
* sourceUrl
* referenceCode
* originalLanguage
* originalExcerpt
* normalizedExcerpt
* notes
* reliabilityLevel
* createdAt
* updatedAt

### StoryDraft-kenttiä vähintään

* id
* caseId
* version
* editorialSummary
* narrativeDraft
* factCheckNotes
* status
* createdAt
* updatedAt

Pidä skeema riittävän normaalimuotoisena, mutta älä ylisuunnittele.

---

## 6. Sisällölliset ja eettiset säännöt

Tämä tuote käsittelee todellisia rikoksia ja historiallisia henkilöitä. Noudata näitä sääntöjä kaikkialla sovelluksessa:

* Älä esitä epävarmaa tietoa faktana.
* Merkitse epävarmuus näkyvästi, jos lähde on tulkinnanvarainen.
* Erota selvästi:

  * lähdeaineistossa näkyvä tieto
  * toimituksellinen tulkinta
  * narratiivinen muotoilu
* Älä glamorisoi väkivaltaa.
* Älä kirjoita clickbait-otsikoita.
* Käytä asiallista, uskottavaa, journalistista sävyä.
* Lisää näkyvä huomautus siitä, että historialliset lähteet voivat sisältää vanhentunutta, loukkaavaa tai epätarkkaa kieltä.
* Varaudu siihen, että myöhemmin järjestelmään tulee lähdekohtainen fact-check workflow.

---

## 7. UX- ja design-ohjeet

Ulkoasun pitää tuntua uskottavalta kulttuuri- ja mediatuotteelta, ei bootstrap-adminilta.

### Visuaalinen suunta

* moderni, rauhallinen, tekstipainotteinen
* paljon whitespacea
* hyvä typografia
* hillitty, tummahko tai neutraali väripaletti
* korttipohjainen selaus listauksissa
* ei liikaa koristeita tai skeumorfismia

### Etusivulla pitää olla

* selkeä arvolupaus
* muutama featured-tapaus
* suodattimiin johtava CTA
* uskottava intro siitä, miten palvelu käyttää arkistolähteitä

### Tapaussivulla pitää olla

* otsikko
* ingressi
* metatiedot
* narratiivinen sisältö
* lähdeosiot
* toimituksellinen huomautus
* mahdolliset tagit

### Adminissa pitää olla

* mahdollisimman tehokas lomakekokemus
* erotetut kentät lähteelle, toimitukselliselle yhteenvedolle ja julkaisusisällölle
* status näkyvästi
* validointivirheet selkeästi

---

## 8. Koodin laatuvaatimukset

Noudata näitä aina:

* kirjoita tyypitettyä TypeScriptiä, älä käytä `any`-tyyppiä ilman vahvaa perustetta
* pidä komponentit pieniä ja tarkoituksenmukaisia
* erottele domain-logiikka käyttöliittymästä
* tee palvelinpuolen dataoperaatiot selkeiksi util- tai service-kerroksiksi
* nimeä asiat selvästi, ei lyhenteitä ilman syytä
* vältä liian syviä hakemistorakenteita
* älä tee ennenaikaista abstraktiota
* lisää kommentteja vain silloin, kun logiikka ei muuten ole ilmeinen
* kirjoita testit logiikalle, ei pelkästään renderöinnille

---

## 9. Hakemistorakenteen tavoite

Codex voi tarkentaa tätä hieman, mutta pidä rakenne suunnilleen tällaisena:

```text
/app
  /(public)
    /page.tsx
    /cases/page.tsx
    /cases/[slug]/page.tsx
  /(admin)
    /admin/page.tsx
    /admin/cases/page.tsx
    /admin/cases/new/page.tsx
    /admin/cases/[id]/edit/page.tsx
/components
  /ui
  /cases
  /layout
/lib
  /db
  /validation
  /services
  /mappers
/prisma
  schema.prisma
  seed.ts
/tests
  /unit
  /e2e
/public
```

Pidä App Router -rakenne johdonmukaisena.

---

## 10. Kehitysjärjestys

Tee työ tässä järjestyksessä, ellei siihen ole hyvää syytä tehdä muutosta:

### Vaihe 1: Perustukset

* alusta Next.js + TypeScript + Tailwind + pnpm
* lisää ESLint, Prettier, Vitest, Playwright
* lisää shadcn/ui-perusrakenne
* luo README
* luo `AGENTS.md`

### Vaihe 2: Domain ja tietokanta

* määritä Prisma schema
* luo migraatio
* tee seed-data
* tee data access -kerros

### Vaihe 3: Julkinen MVP

* etusivu
* tapauslista
* tapaussivu
* suodatus query-parametreilla

### Vaihe 4: Admin MVP

* admin-layout
* tapauslomake
* lähdeviitteiden syöttö
* draftin syöttö
* validointi

### Vaihe 5: Testit ja viimeistely

* yksikkötestit mappereille/validoinnille
* vähintään yksi e2e-polku
* a11y- ja virhetilojen siistiminen
* README:n päivittäminen

Älä yritä tehdä kaikkea yhdellä kertaa, mutta jätä aina seuraavaa vaihetta varten siisti jatkopiste.

---

## 11. Definition of Done

Tehtävä ei ole valmis ennen kuin nämä täyttyvät:

* projekti käynnistyy paikallisesti
* lint menee läpi
* tyyppitarkistus menee läpi
* testit menevät läpi tai puuttuvat kohdat on dokumentoitu täsmällisesti
* README kertoo miten projekti käynnistetään
* ympäristömuuttujille on `.env.example`
* demoaineisto mahdollistaa sovelluksen käytön heti
* käyttöliittymässä ei ole ilmeisiä rikkinäisiä polkuja

---

## 12. Suoritus- ja SEO-periaatteet

* käytä server components -mallia oletuksena
* minimoi client-komponentit
* lisää perustason metadata
* tee URL-rakenne selkeäksi
* käytä semanttista HTML:ää
* vältä raskaita riippuvuuksia, jos perusratkaisu riittää

---

## 13. Tietoturva ja ylläpidettävyys

* älä koskaan commitoi salaisuuksia
* lisää `.env.example`
* validoi kaikki admin-lomakkeiden syötteet Zodilla
* käsittele virheet niin, ettei sisäisiä poikkeuksia vuoda käyttöliittymään
* valmistele rakenne niin, että myöhemmin voidaan lisätä oikea autentikointi ilman suurta refaktorointia

---

## 14. Mitä ei pidä tehdä

* älä rakenna monimutkaista mikroarkkitehtuuria
* älä lisää raskasta WYSIWYG-editoria ensimmäisessä versiossa
* älä tee AI-generointia pakolliseksi MVP:ssä
* älä käytä mock-dataa lopullisena ratkaisuna, jos tietokanta on jo mukana
* älä tee UI:sta liian synkkää, sensaatiomaista tai pelillistettyä
* älä jätä testikomentojen tai käynnistysohjeiden toimivuutta tarkistamatta

---

## 15. Ensimmäisen version onnistumiskriteerit

Ensimmäinen julkaistava versio onnistuu, jos:

* ylläpitäjä voi syöttää vähintään yhden tapauksen lähteineen
* käyttäjä voi selata tapauksia
* käyttäjä voi avata yksittäisen tapauksen
* käyttäjä näkee, mihin lähteisiin tapaus perustuu
* koodi on riittävän siistiä jatkokehitykseen

---

## 16. README:n minimirakenne

Codexin pitää luoda README, jossa on vähintään:

* projektin kuvaus
* tech stack
* asennus
* environment variables
* tietokannan käynnistys / prisma-komennot
* seed-datan käyttö
* kehityskomennot
* testikomennot
* roadmap

---

## 17. Käynnistysprompt Codexille

Käytä tämä ensimmäisenä varsinaisena tehtävänantona Codexille:

> Luo uusi projekti nimeltä Arkisto-crime alkaen täysin tyhjästä repositoriosta. Rakenna tuotantokelpoinen MVP historiallisten true crime -tapausten julkaisuun. Käytä Next.js App Routeria, TypeScriptiä, Tailwindia, shadcn/ui:ta, Prismaa, SQLitea devissä, Zodia, React Hook Formia, Vitestiä ja Playwrightia. Toteuta julkinen etusivu, tapauslista, tapaussivu, admin-puolen tapauslomake, Prisma-skeema malleille Case, SourceReference ja StoryDraft, seed-data, `.env.example`, README sekä toimivat lint-, typecheck- ja testikomennot. Erota lähdeaineisto, toimituksellinen tiivistelmä ja julkaistu narratiivi toisistaan sekä käyttöliittymässä että tietomallissa. Tee käyttöliittymästä uskottava, rauhallinen ja toimituksellinen. Aloita perustuksista, etene vaiheittain, ja varmista että projekti käynnistyy paikallisesti.

---

## 18. Jatkokehityspromptit

### Prompt 2: adminin viimeistely

> Paranna admin-käyttöliittymää niin, että tapausten luonti ja muokkaus onnistuvat sujuvasti. Lisää listaus olemassa olevista tapauksista, muokkausnäkymä, poistotoiminto varmistuksella ja selkeä status workflow draft/review/published.

### Prompt 3: lähdeuskottavuus

> Lisää tapaussivulle lähdeosiot, joissa näkyy arkistoviite, alkuperäinen kieli, normalisoitu ote ja toimituksellinen huomautus epävarmuudesta. Varmista, että epävarma tieto ei näy ehdottomana faktana.

### Prompt 4: haku ja suodatus

> Lisää tapauslistaukseen URL-pohjainen suodatus ja tekstihaku. Suodattimien tulee toimia palvelinpuolella ja URL:n pitää olla jaettava.

### Prompt 5: julkaisuvalmius

> Tee sovelluksesta julkaisuvalmiimpi: paranna metadataa, virhesivuja, tyhjien tilojen UX:ää, perus-a11y-tarkistuksia ja testikattavuutta.

---

## 19. Käytännön työskentelysääntö Codexille

Kun toteutat tehtävää:

1. suunnittele ensin tiedostorakenne ja riippuvuudet
2. alusta perusprojekti
3. lisää domain-malli ja tietokanta
4. toteuta sivut
5. toteuta admin
6. lisää testit
7. aja kaikki tarkistukset
8. päivitä README

Jos jokin osa jää kesken, priorisoi toimiva kokonaisuus dokumentoiduin puuttein.

---

## 20. Lopputuloksen muoto

Lopputuloksen pitää olla repo, jonka kuka tahansa kehittäjä voi kloonata, käynnistää ja ymmärtää ilman lisäselvityksiä.

Tavoite ei ole demo, vaan uskottava perusta oikealle tuotteelle.

---

# Lisäohjeistus: Kansallisarkisto MCP server

Alla on suoraan Codexille annettava ohjeistus Kansallisarkistoa varten.

## Tavoite

Rakenna tuotantokelpoinen **MCP-palvelin Kansallisarkiston historiallisten aineistojen hakuun**. Palvelimen tehtävä on tarjota LLM-asiakkaille turvallinen, hyvin dokumentoitu ja testattu rajapinta historiallisten tuomiokirjojen ja niihin liittyvän metadatan hakemiseen.

Palvelimen pitää ensivaiheessa tukea vähintään seuraavia käyttötapauksia:

* yleiskuva saatavilla olevasta datasta
* yksinkertainen tekstihaku historiallisista oikeusasiakirjoista
* haku paikan ja aikavälin perusteella
* samankaltaisten dokumenttien haku
* indeksien metadatan ja kenttien tarkastelu
* kehittyneempi raakakysely niille käyttäjille, jotka tarvitsevat tarkempaa hakua

Tavoite ei ole demo, vaan oikea integraatiopalvelin, joka voidaan liittää MCP-yhteensopiviin asiakkaisiin.

## Teknologiapäätökset

Käytä oletuksena seuraavia valintoja:

* **Kieli:** TypeScript
* **Runtime:** Node.js 20+
* **Package manager:** pnpm
* **MCP SDK:** virallinen TypeScript MCP SDK
* **HTTP framework:** Hono tai Express; suosi Honoa, jos se tekee toteutuksesta kevyemmän
* **Validointi:** Zod
* **Testit:** Vitest
* **Lint/format:** ESLint + Prettier
* **Build:** tsup tai tsx + tsc
* **Transportit:** toteuta vähintään
  * `stdio`
  * `Streamable HTTP`

## Toteutettavat MCP-työkalut

Toteuta vähintään nämä työkalut. Nimeä ne vakioidusti, dokumentoi huolellisesti ja anna jokaiselle tarkka Zod-skeema.

1. `get_data_overview`
2. `get_index_info`
3. `get_index_mappings`
4. `simple_text_search`
5. `search_by_location_and_time`
6. `search_similar_documents`
7. `search_index`

## Konfiguraatio

Luo `.env.example` ainakin näillä:

```env
KANSALLISARKISTO_BASE_URL=
KANSALLISARKISTO_API_KEY=
MCP_TRANSPORT=stdio
PORT=8000
LOG_LEVEL=info
REQUEST_TIMEOUT_MS=15000
MAX_RESULT_SIZE=20
```

## Definition of Done

Tehtävä on valmis vasta kun:

* projekti buildaa ilman virheitä
* lint menee läpi
* testit menevät läpi
* `stdio` toimii
* `Streamable HTTP` toimii
* kaikki 7 työkalua on toteutettu
* README on käyttökelpoinen
* `.env.example` on mukana
* virheet palautuvat hallitusti
* palvelin voidaan liittää MCP-asiakkaaseen

## Kansallisarkiston MCP-palvelimen osoite

https://sisaltohaku.demo.kansallisarkisto.fi/mcp/
