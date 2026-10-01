# CLAUDE.md — Violin Lab: app per aprendre les notes del violí

## Què és aquest projecte
App web d'ús personal per a una alumna de violí que fa uns 3 anys que toca i ara treballa el **Stradivari Vol. 2** (J. Alfaras, Ed. Boileau). Serveix per aprendre:
- On es posa cada dit a 1a posició, segons la digitació que treballa al llibre.
- El nom de cada nota a cada corda.
- La lectura en clau de sol, i a passar del pentagrama al diapasó i a l'inrevés.

És una eina d'entrenament diari amb sessions curtes i repetició dels errors. No és un curs.

Idioma de la interfície: **català**. Notació: **llatina per defecte** (Do, Re, Mi…), com al llibre; a la configuració es pot triar anglesa (C, D, E…) o barrejada.

## Tecnologia
- Un sol fitxer `index.html` amb HTML, CSS i JavaScript vanilla.
- Sense backend, sense build, sense frameworks i sense dependències externes (ni CDN).
- Ha de funcionar obrint el fitxer directament al navegador.
- Responsive: ordinador, tauleta i mòbil, amb botons i caselles prou grans per tocar amb el dit.
- Persistència amb `localStorage` (claus amb prefix `violinlab.`): configuració, progrés i estadístiques.
- So amb la Web Audio API, generat per síntesi. Sense samples externs.

## Disseny
- La maqueta de referència és `propostes/index.html`, amb tres pells:
  - **A · Fusta i paper**
  - **B · Escenari**
  - **C · Pop**
- **Les tres pells es mantenen a l'app** com a utilitat: hi ha un botó per canviar de pell i la tria es desa (`violinlab.proposta`). No hi ha tema clar/fosc a part: cada pell ja en defineix un.
- Les fotos de `referencia/` són les pàgines 40–42 del Stradivari Vol. 2 ("Evolució de les digitacions"). Serveixen de referència musical i visual, però no se'n copien imatges, logotips ni textos.
- El diapasó és el protagonista i es dibuixa com al llibre:
  - En horitzontal, amb la voluta a l'esquerra i la corda **Mi a dalt** i la **Sol a baix**.
  - Caselles per **semitò** (de +0 a +7), amb el número de dit a sobre de cada casella.
  - Els punts de les notes porten el nom a dins.
- Els colors per tipus de dit segueixen la lògica del llibre, adaptats a cada pell:
  - corda a l'aire (vermell)
  - dit "normal" (verd)
  - 2 baix (blau)
  - 3 alt (taronja)
  - 1 baix (rosa)
- Les caselles que no corresponen a la digitació triada queden apagades.
- Moviment només com a resposta a una acció, curt (150–400 ms) i desactivat amb `prefers-reduced-motion`. Sense confeti, celebracions exagerades ni ratxes.
- En aquest projecte, la part del violí on es posen els dits es diu **diapasó**.

## Referència musical (verificar sempre contra aquesta taula)

### Cordes a l'aire (altura real, notació científica, MIDI)
| Corda | Nota | MIDI | Freqüència aprox. |
|---|---|---|---|
| Sol (4a) | Sol3 (G3) | 55 | 196,0 Hz |
| Re (3a) | Re4 (D4) | 62 | 293,7 Hz |
| La (2a) | La4 (A4) | 69 | 440,0 Hz |
| Mi (1a) | Mi5 (E5) | 76 | 659,3 Hz |

- Nota a una casella: `MIDI = MIDI_corda_aire + semitons`.
- Freqüència: `f = 440 × 2^((MIDI − 69) / 12)`.
- El violí **no transposa**: sona tal com s'escriu.

### Noms de notes
| Llatí | Do | Re | Mi | Fa | Sol | La | Si |
|---|---|---|---|---|---|---|---|
| **Anglès** | C | D | E | F | G | A | B |

♯ = sostingut, ♭ = bemoll (Do♯ = C♯, Si♭ = B♭).

### Dits a 1a posició (semitons sobre la corda a l'aire)
| Semitons | Dit | Color | Sol | Re | La | Mi |
|---|---|---|---|---|---|---|
| +0 | 0 (aire) | vermell | Sol | Re | La | Mi |
| +1 | 1 baix | rosa | La♭ | Mi♭ | Si♭ | Fa |
| +2 | 1 | verd | La | Mi | Si | Fa♯ |
| +3 | 2 baix | blau | Si♭ | Fa | Do | Sol |
| +4 | 2 alt | verd | Si | Fa♯ | Do♯ | Sol♯ |
| +5 | 3 | verd | Do | Sol | Re | La |
| +6 | 3 alt | taronja | Do♯ | Sol♯ | Re♯ | La♯ |
| +7 | 4 | verd | Re | La | Mi | Si |

- Grafia: el dit baix s'escriu amb bemoll i el dit alt amb sostingut, tal com fa el llibre. A l'etapa "1a posició completa" s'accepten les dues grafies enharmòniques (Do♯ = Re♭…).
- El 4t dit (+7) sona igual que la corda a l'aire següent. Si la pregunta no diu la corda (mòdul 4), s'accepten totes dues posicions; si la diu (mòdul 2), només compta la d'aquella corda.

### Digitacions (etapes del llibre)
| Etapa | Nom | Semitons actius |
|---|---|---|
| Stradivari 1 | 0-1-23-4 | 0, 2, 4, 5, 7 |
| Stradivari 2 | 0-12-3-4 | 0, 2, 3, 5, 7 |
| Stradivari 2 | 0-1-2-34 | 0, 2, 4, 6, 7 |
| Stradivari 2 | 0-1223-4 | 0, 2, 3, 4, 5, 7 |
| Stradivari 2 | 0-122334 | 0, 2, 3, 4, 5, 6, 7 |
| Stradivari 3 | 1a posició completa | 0, 1, 2, 3, 4, 5, 6, 7 |

Es poden triar una o més digitacions alhora. Per defecte: **0-12-3-4** i **0-1-2-34** (Stradivari 2).

## Partitura en clau de sol
- Pentagrama dibuixat amb **SVG propi**, sense llibreries.
- Línies (de baix a dalt): Mi4, Sol4, Si4, Re5, Fa5. Espais: Fa4, La4, Do5, Mi5.
- Per sota: Re4 (espai sota el pentagrama), Do4 (1a línia addicional), Si3, La3 (2a línia addicional), Sol3 (sota la 2a línia addicional, corda Sol a l'aire).
- Per sobre: Sol5 (espai), La5 (1a línia addicional), Si5 (espai sobre la 1a addicional, 4t dit a la corda Mi).
- Dibuixa les línies addicionals que calguin i les alteracions a l'esquerra de la nota.
- Rang a 1a posició: de Sol3 (MIDI 55) a Si5 (MIDI 83).

## Mòduls d'exercici
1. **Diapasó → nota**: es marca una posició al diapasó i l'alumna tria el nom de la nota.
2. **Nota → diapasó**: surt un nom de nota i la corda ("Corda La: Si") i l'alumna la toca al diapasó.
3. **Partitura → nota**: surt una nota en clau de sol i l'alumna diu el nom.
4. **Partitura → diapasó**: surt una nota en clau de sol i l'alumna la toca al diapasó, a l'altura exacta.
5. **Digitació**: es mostra una nota (al pentagrama o pel nom, i la corda) i l'alumna diu quin dit és (0, 1 baix, 1, 2 baix, 2 alt, 3, 3 alt, 4).

### Eines sense preguntes
- **Toca lliure**: en tocar una casella del diapasó sona la nota i es mostren el nom, el dit i la nota al pentagrama.

## Configuració
- **On sóc del llibre**: digitacions actives (vegeu la taula).
- Activar i desactivar cordes concretes.
- Notació: llatina (per defecte), anglesa o barrejada.
- So activat o desactivat.
- Pell: A, B o C.
- Cintes de principiant al diapasó (dits 1, 2 alt i 3), com a la maqueta: activades o no.
- Mida de sessió: 10, 20 o 50 preguntes, o mode lliure.

## Sistema d'aprenentatge
- Cada combinació (posició o nota × mòdul) té un pes. Els errors i les respostes lentes n'augmenten el pes i els encerts ràpids el redueixen. Les preguntes se sortegen segons el pes.
- No es repeteix la mateixa pregunta dues vegades seguides.
- Retorn immediat:
  - Si és correcte: marca verda, so de la nota i pas a la següent.
  - Si és incorrecte: es mostra la resposta correcta al diapasó i/o al pentagrama i sona la nota correcta. To amable, sense penalitzacions visibles.
- Estadístiques:
  - Percentatge d'encerts per mòdul.
  - Temps mitjà de resposta.
  - **Mapa de calor del diapasó** amb les posicions on es falla més.
  - Historial de sessions (data, mòdul, encerts).
- Opció per reiniciar les estadístiques, amb confirmació.
- Sense ratxes.

## So
- Ha de sonar a l'**altura real** i ser afinat (±2 cèntims, verificable per autocorrelació).
- Timbre de violí per síntesi, sense samples. Proposta: so d'arc (oscil·lador en dent de serra amb ressonàncies de caixa filtrades, atac suau i vibrato lleuger). El model concret es decidirà a la fase 4.
- L'àudio s'activa amb la primera interacció de l'usuari (restricció dels navegadors).

## Com treballar en aquest projecte
1. **Abans d'escriure codi**, proposa l'estructura i un esbós de la interfície, i espera validació.
2. Implementa per fases:
   - **Fase 1**: diapasó + configuració (digitacions, cordes, pells) + mòduls 1 i 2 + toca lliure + persistència.
   - **Fase 2**: pentagrama SVG + mòduls 3 i 4.
   - **Fase 3**: mòdul 5 (digitació).
   - **Fase 4**: estadístiques, mapa de calor i so afinat.
3. Al final de cada fase, verifica el càlcul de notes amb la taula de referència: comprova almenys les cordes a l'aire, el 4t dit de cada corda, el 2 baix i el 3 alt, i les notes escrites Sol3, Do4 i Si5 al pentagrama.
4. No afegeixis dependències ni funcionalitats fora d'aquest document sense preguntar.

## Fora d'abast (de moment)
- Altres posicions (2a, 3a…).
- Detecció de notes pel micròfon.
- Cançons, partitures o contingut del llibre amb drets d'autor.
- Comptes d'usuari, sincronització o backend.
