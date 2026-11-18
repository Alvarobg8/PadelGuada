# Projecte Integrador · Lliurament 1

_Proposta de projecte i organització del treball (Parts A i C) · Club de Pàdel_

_Alumne/a: Alvaro Bria Gimeno

## Part A: proposta de projecte

### 1. Nom i descripció

**PàdelGuada** és la base de dades del **Club de Pàdel de Guadassuar**, un club privat (entitat fictícia) amb sis pistes, quatre de cobertes i dues de descobertes, uns 300 socis i una xicoteta escola de classes dirigides. El client és la gerència del club. Ara les reserves es porten en una agenda de paper i un grup de WhatsApp, i les quotes en un full de càlcul. Això provoca dobles reserves de la mateixa pista, quotes que ningú reclama, classes amb més alumnes que places i cap control de qui entra al club. PàdelSerra centralitzarà socis, quotes, reserves, classes i torneigs, aplicarà les regles del club i protegirà les dades personals segons el RGPD.

### 2. Usuaris del sistema

| Rol | Què necessita consultar o modificar |
|---|---|
| Recepcionista | Dona d'alta socis i carnets, crea i cancel·la reserves (en mostrador o per telèfon), registra pagaments de quotes i inscriu socis a les classes. |
| Monitor | Consulta les llistes de socis inscrits a les seues classes i el seu calendari. No pot veure dades bancàries. |
| Gerent | Consulta informes d'ocupació de pistes, quotes impagades i estat dels torneigs. Gestiona monitors, pistes, tarifes i classes. |
| Soci | Consulta i cancel·la les seues reserves, veu les seues quotes i s'inscriu a classes i torneigs. |

### 3. Informació a guardar

| Cosa | Exemples de dades |
|---|---|
| 1. Persona | DNI, nom, cognoms, telèfon, correu, data de naixement |
| 2. Soci | número de soci, data d'alta, nivell de joc, modalitat de quota, compte bancari (IBAN), tutor legal si és menor, observacions mèdiques |
| 3. Monitor | titulació, preu per hora, data de contractació |
| 4. Carnet d'accés | codi, data d'emissió, estat (actiu o bloquejat) |
| 5. Pista | número, tipus (coberta o descoberta), superfície, estat |
| 6. Reserva | data, hora d'inici, durada, tipus de franja (punta o vall), preu aplicat, estat (confirmada, cancel·lada, cancel·lada amb recàrrec) |
| 7. Quota | període (mes i any), import, data de venciment, estat |
| 8. Pagament | número de pagament dins de la quota, data, import, mètode (targeta, domiciliació o efectiu) |
| 9. Classe | data, hora, nivell, places màximes, preu |
| 10. Torneig | nom, dates, categoria, places màximes, data límit d'inscripció |
| 11. Inscripció a classe | data d'alta, estat (confirmada o cancel·lada) |
| 12. Inscripció a torneig | data d'inscripció, categoria, posició final |

_Les coses 1 a 10 seran entitats; la 11 i la 12 són dades de les relacions entre soci i classe, i entre soci i torneig._

### 4. Regles de funcionament

| Núm. | Regla |
|---|---|
| R1 | Una pista no es pot reservar dues vegades en la mateixa data i franja horària. |
| R2 | Cada reserva dura 90 minuts. Les franges comencen a les 09:00 i l'última acaba a les 22:30. |
| R3 | Un soci amb alguna quota vençuda i sense pagar no pot fer reserves. |
| R4 | Un soci pot tindre com a màxim 3 reserves futures actives alhora. |
| R5 | Una reserva es pot cancel·lar sense cost fins a 24 hores abans. Després, es cobra el 50% del preu. |
| R6 | Una classe té entre 4 i 8 places i no admet més inscripcions quan s'omple. |
| R7 | Un monitor no pot tindre dues classes que se solapen en l'horari, i una pista no pot tindre una classe i una reserva en la mateixa franja. |
| R8 | Un pagament sempre pertany a una quota, i la suma dels pagaments d'una quota no pot superar el seu import. |
| R9 | Un soci menor de 18 anys ha de tindre un tutor legal registrat amb autorització signada. |
| R10 | Cada soci té un únic carnet d'accés actiu, i un carnet pertany a un sol soci. |
| R11 | Un torneig no admet inscripcions després de la data límit ni quan ha arribat al màxim de places. |
| R12 | El preu d'una reserva depén de la franja (punta o vall) i es conserva el preu aplicat, encara que la tarifa canvie després. |

### 5. Checklist de complexitat

| Element obligatori | On apareix en PàdelSerra |
|---|---|
| Entre 8 i 10 entitats | 10 entitats: Persona, Soci, Monitor, Carnet d'accés, Pista, Reserva, Quota, Pagament, Classe i Torneig. |
| Dues relacions N:M, una amb atributs propis | Soci–Classe (inscripció, amb data d'alta i estat) i Soci–Torneig (inscripció, amb data, categoria i posició final). |
| Una relació 1:1 | Soci–Carnet d'accés (R10). |
| Una entitat feble | Pagament: no existeix sense la quota a què pertany (R8). |
| Una generalització / especialització | Persona → Soci / Monitor. |
| Una relació reflexiva o ternària | Ternària: Monitor–Classe–Pista (qui imparteix cada classe i en quina pista). |
| Dades amb dimensió temporal | Data i franja de cada reserva, històric de quotes i pagaments, preu aplicat en cada reserva (R12). |
| Una operació crítica amb risc de concurrència | Dues persones (recepció i un soci) reserven la mateixa pista i franja alhora (R1). |
| Dades personals (RGPD) | DNI, telèfon, correu, data de naixement, IBAN, dades de menors i tutors, observacions mèdiques. |
| Una part candidata a NoSQL | Registre d'accessos al club amb el carnet (gran volum). |

### 6. Dades personals i part NoSQL

**Dades personals (RGPD).** El club guardarà DNI, nom, telèfon, correu i data de naixement de tots els socis i monitors, el compte bancari (IBAN) dels socis amb domiciliació, les dades del tutor legal dels menors i, si el soci ho indica, observacions mèdiques (lesions o limitacions), que són dades de salut i necessiten el consentiment explícit. Només es guardarà el que cal. El compte bancari només el veuran recepció i gerència, i els monitors no; les observacions mèdiques només les veuran gerència i el monitor de la classe del soci.

**Part NoSQL.** El **registre d'accessos** és el millor candidat per a MongoDB: cada vegada que un soci passa el carnet pel torniquet s'afig un registre (carnet, data i hora, punt d'accés), són milers d'entrades al dia, només s'afigen i es consulten per soci i data. La resta (socis, reserves, quotes, pagaments) es queda en MySQL perquè necessita integritat referencial i transaccions.

## Part C: organització del treball

| Apartat | Contingut |
|---|---|
| Repositori | Repositori Git privat amb el professor com a col·laborador: [enllaç al repositori]. Estructura: *docs/* (00_proposta.md i memòries), *modelatge/*, *sql/* (DDL, DML i DCL), *nosql/*, *dades/* i README.md. Etiqueta *v0.1-proposta*. |
| Tauler | Tauler a [GitHub Projects / Trello / Taiga]: [enllaç al tauler]. Columnes Pendent · En curs · En revisió · Fet, amb els 24 elements del backlog carregats. |
| Definition of Done | **General:** un element està fet quan (1) compleix el criteri «Com sabré que està feta», (2) s'ha provat amb dades de prova, (3) el resultat està pujat al repositori amb un commit descriptiu, (4) la documentació afectada està actualitzada i (5) està a la columna Fet del tauler.<br>**Punt propi:** (6) tota regla de negoci implementada s'ha comprovat amb un cas que la compleix i un altre que no la compleix. |
| Planificació personal | Dimarts i dijous 1 h 30 a la vesprada, i dissabte 2 h: unes 5 hores per setmana fora de l'aula. Cada diumenge revise el tauler i preparo la setmana següent. |
| Riscos | **1. Abast massa gran.** Prioritze els elements imprescindibles i deixe els desitjables (HU-11, HU-14, HT-09) per al final.<br>**2. Perdre treball.** Faig commits almenys dues vegades per setmana i tinc una còpia al núvol.<br>**3. Regles ambigües.** Les valide amb el professor (client) en les tutories abans de la Fita 1.<br>**4. Retards acumulats.** Revise el tauler cada setmana i, si em quede arrere, reduïsc l'abast de l'sprint. |
