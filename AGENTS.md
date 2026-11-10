# CLAUDE.md

Pràctica de Videojocs (VJ, FIB-UPC), en grups de dos: la nostra versió del **Pocket Bomberman** (Game Boy Color), feta sobre el framework "02-Bubble" de laboratori (C++ / OpenGL / GLFW / GLEW / GLM / SOIL).

Comunica't amb l'usuari en **català**. Als comentaris del codi mantén l'estil existent (anglès, breus).

## El joc

Plataformes 2D per a un sol jugador. Bomberman es mou a esquerra/dreta i salta. Les seves bombes **no cauen per gravetat**: queden suspeses a l'aire on es deixen i es poden fer servir com a plataformes/escales (amb el risc que esclatin). Cada nivell es supera eliminant tots els enemics i arribant a la porta.

## Requisits de l'enunciat

L'estat de treball es manté a [checklist.md](2DGame/02-Bubble/02-Bubble/checklist.md).
La llista següent recull els requisits de l'enunciat; consulta aquella checklist
per al seguiment actual. El catàleg vigent de peces és [blocs.md](2DGame/02-Bubble/02-Bubble/blocs.md)
i el format dels mapes és [levels/README.md](2DGame/02-Bubble/02-Bubble/levels/README.md).

Si falten les funcionalitats bàsiques d'un joc (objectius, guanyar/perdre, tornar a començar) la nota és un **zero**. Cada bug detectat resta punts, així que cal prioritzar la robustesa per sobre de les funcionalitats a mig fer.

L'abast obligatori és de **cinc nivells i un boss a l'últim**. Les cinc Pedres de Poder, els cinc mons i els bosses de cada món descriuen el joc original a la introducció de l'enunciat; no són funcionalitats addicionals exigides a la pràctica. El cor es menciona com a protecció davant dels enemics, però no s'exigeix explícitament entre els quatre objectes: els nostres són Bomb Up, Fire Up, Speed Up i VEST.

### Part bàsica (6 punts)
- [ ] 4 pantalles: **menú principal, jugar, instruccions, crèdits**.
- [ ] **5 nivells**: cal eliminar tots els enemics i arribar a la porta per superar-los.
- [x] Bomberman es mou a esquerra/dreta i salta.
- [ ] **3 enemics** amb comportaments diferents. Si toquen Bomberman, perd una vida (excepte si té el «cor»).
- [ ] **3 vides**; perdre-les totes = game over i es torna al **menú**.
- [ ] Poder tornar a jugar després de guanyar o perdre: comprovar el retorn al menú i l'inici d'una altra partida sense errors ni estat residual indegut.
- [ ] **Bombes** que esclaten i poden matar enemics i el mateix Bomberman. Sempre apareixen **alineades al centre d'un tile**.
- [ ] Bomberman pot **saltar sobre les seves bombes** per arribar a zones elevades.
- [ ] **Tiles destructibles** (p.ex. arbustos) que en desaparèixer poden deixar objectes.
- [ ] **4 objectes** (power-ups), dos dels quals obligatoris: **Bomb Up** (+1 bomba simultània) i **Fire Up** (+mida d'explosió). Un altre candidat natural és el **cor** (protegeix d'un cop).
- [ ] **Temps límit** per nivell.
- [ ] **Boss** a l'últim nivell (basat en els del joc original; si és diferent, cal consultar-ho amb el professor).
- [ ] **HUD** amb com a mínim: temps restant, vides, enemics restants, bombes simultànies i mida de l'explosió.
- [ ] Tecles de depuració:
  - `G`: **god mode**, s'ha de poder **activar i desactivar** (toggle, no mantenir premut).
  - `K`: elimina tots els enemics restants i **obre la porta**.
  - `1`–`5`: carrega directament el nivell corresponent.

### Polish (4 punts)
- So: **música de fons** i **efectes**.
- Totes les entitats amb **animacions** per a cada esdeveniment; evitar canvis bruscos, fer servir **transicions** entre estats/pantalles.
- Revisar també destrucció d'arbustos, recollida d'objectes, obertura de portes i canvis d'estat; tenir les morts dels enemics animades no completa per si sol aquest requisit.
- **Game feel**: revisar moviment, salts, ús de les bombes, dificultat, diversió i claredat del feedback respecte de l'original.

### Punts a validar respecte de l'enunciat

- **Retorn després del game over:** actualment es mostra «YOU ARE DEAD» i cal prémer `Esc` per tornar al menú. L'enunciat exigeix tornar al menú, sense especificar expressament si ha de ser automàtic. S'ha proposat una pantalla de derrota breu amb retorn automàtic per evitar ambigüitats; aquest canvi encara no està implementat.
- **Vida extra blanca:** el joc comença amb 3 vides i permet obtenir-ne una quarta amb «1 UP». L'enunciat parla de tres vides disponibles i no prohibeix explícitament les vides extra; queda pendent confirmar-ne l'acceptació amb el professor.

### Lliurament
`NomCognoms.zip` (≤ 200 MB) amb:
- `info.txt`: noms dels integrants, funcionalitats implementades, instruccions del joc.
- `Binari/`: executable + tots els recursos (images, levels, shaders, DLLs…) perquè funcioni a qualsevol màquina.
- `Projecte/`: projecte compilable amb tot el codi font.
- `demo.avi`: vídeo d'~1 minut, **≤ 100 MB**.

Abans del lliurament, provar el paquet `Binari/` en una altra màquina o en un entorn sense les dependències de desenvolupament instal·lades. Comprovar els recursos, les DLLs i les rutes relatives, i verificar que `Projecte/` es pot compilar amb les instruccions incloses. El vídeo ha de mostrar les funcionalitats treballades.

Com que l'executable carrega recursos amb rutes relatives (`levels/`, `images/`, `shaders/`), qualsevol recurs nou ha d'anar dins de `2DGame/02-Bubble/02-Bubble/` i s'ha de copiar a `Binari/` en el lliurament.

## Estructura

```
2DGame/02-Bubble/02-Bubble.sln          Solució Visual Studio (Win32)
2DGame/02-Bubble/02-Bubble/             Codi font + recursos (és el working dir en executar)
    main.cpp        Bucle principal GLFW, callbacks d'input
    Game.*          Singleton; guarda l'estat de les tecles (getKey) i té la Scene
    Scene.*         Mapa, jugador, enemics, HUD, càmera; update/render
    Player.*        Moviment, salt, col·lisions, vides, invulnerabilitat
    enemies/        Enemy (base), Walker, Runner, Shooter, Boss i Projectile
    HUD.*           Interfície en espai de pantalla (no es mou amb la càmera)
    TileMap.*       Carrega levels/*.txt i fa les col·lisions amb tiles
    Sprite.*, Texture.*, Shader*.*, AnimKeyframes.h   Utilitats del framework
    images/  levels/  shaders/
2DGame/02-Bubble/Debug|Release/         Executables + DLLs (glew32, freetype6) necessàries
libs/                                   GLFW, GLEW, GLM, SOIL, FreeType
.vscode/                                Tasques de build/debug per VS Code
```

### Convencions del codi
- Afegir enemics nous com a subclasses d'`Enemy` (com `Walker`) i registrar-los al `vector<Enemy*>` de `Scene`.
- El HUD no té estat propi del joc: `Scene` li passa els valors a cada `update`.
- L'input es llegeix fent polling amb `Game::instance().getKey(...)`. Per a accions d'una sola pulsació (toggles, posar bomba, canviar nivell) cal detectar el flanc (premut ara i no abans) o fer-ho a `Game::keyPressed`, per no repetir l'acció cada frame.
- Format de nivell (`levels/levelXX.txt`): capçalera `TILEMAP`, mida en tiles, tile size i block size, tilesheet, nombre de tiles del tilesheet, i després la graella. Consulta `blocs.md` i `levels/README.md` per distingir blocs estàtics i marcadors especials com `a`, `T`, `U` o `C`.
- Tabuladors per indentar, estil del framework (claus en línia pròpia).
- Afegir els fitxers nous `.cpp/.h` al `02-Bubble.vcxproj` (i `.filters`) perquè compilin amb MSBuild.

### Blocs de `personalized.png`: instruccions per a la IA

- L'atles actual `2DGame/02-Bubble/02-Bubble/images/personalized.png` fa **80×80 píxels**: **5 columnes × 5 files** de tiles de **16×16**. Els índexs comencen a 1 i es compten d'esquerra a dreta i de dalt a baix. Les coordenades següents són la cantonada superior esquerra, en píxels.
- Els nivells que fan servir aquest atles han de declarar `5 5` a la capçalera: `TileMap::prepareArrays` calcula les coordenades de textura a partir d'aquests valors. Si canvia l'atles, revisa tots els nivells que l'utilitzen i comprova el resultat visual.

| Índex | Codi al mapa | Píxel (x, y) | Peça i ús |
| --- | --- | --- | --- |
| 1–3 | `1`, `2`, `3` | (0, 0), (16, 0), (32, 0) | Terra sòlida. |
| 4 | `*`, `B`, `F`, `S`, `P`, `K` | (48, 0) | Arbust destructible, buit o amb objecte. |
| 5 | `5` | (64, 0) | Pedra indestructible. |
| 6 | `K` | (0, 16) | Clau amagada en un arbust. |
| 7 | `L` | (16, 16) | Porta amb pany. |
| 8 | `D` | (32, 16) | Sortida tancada. |
| 9 | `D`, `L` oberta | (48, 16) | Porta oberta. |
| 10 | `a` | (64, 16) | Plataforma de maons temporal. |
| 11–12 | `b`, `c` | (0, 32), (16, 32) | Fotogrames dels maons; estàtics si s'escriuen al mapa. |
| 13 | `d` | (32, 32) | Terra / pedra clara sòlida. |
| 14 | — | (48, 32) | Bloc gris antic, sense ús. |
| 15–17 | — | (64, 32), (0, 48), (16, 48) | Explosió antiga, sense ús. |
| 18 | — | (32, 48) | Cel decoratiu. |
| 19–20 | `C` | (48, 48), (64, 48) | Núvol de 2×1 tiles, sense col·lisió. |
| 21–23 | `T` | (0, 64), (16, 64), (32, 64) | Trampolí: 9, 12 i 16 píxels d'alçada. |
| 24–25 | `U`, `V` | (48, 64), (64, 64) | Botó preparat i comprimit; `U` de paret, `V` girat 90° al terra. Sense col·lisió. |

- Els codis de tile positius creen **blocs estàtics, sòlids i indestructibles**, excepte `a`, que té la mecànica següent. `4` no crea un arbust destructible i `6`, `7`, `8` i `9` no creen clau ni portes funcionals. Els codis `a`, `b`, `c` i `d` són minúscules: les majúscules són marcadors d'entitats o objectes.
- Els `a` s'activen només quan el jugador els trepitja per sobre: esperen 0,5 segons amb la textura completa i mostren les peces 10→11→12 amb parpelleig durant 1 segon més, mantenint la col·lisió durant aquests 1,5 segons. Es mantenen absents 1,5 segons i reapareixen amb 12→11→10 i parpelleig durant 1 segon. Recuperen la col·lisió en començar la reaparició, esperant abans si la casella conté el jugador, un enemic o una bomba. El parpelleig té un cicle de 360 ms (50% visible, 50% invisible). Trepitjar-los durant la reaparició inicia el compte immediatament sense interrompre l'animació inversa; sense jugador a sobre es mantenen sòlids. El compte no es reinicia en tornar-los a trepitjar mentre estan actius. Les explosions no els destrueixen.
- Les plataformes `a` són a les posicions de cada graella des del començament i estan subjectes a aquesta mecànica. Consulta els `levelXX.txt` per a la distribució actual. No hi ha blocs grisos explosius ni enllaços de moviment.
- `*` és un arbust buit; `B`, `F`, `S` i `P` amaguen, respectivament, Bomb Up, Fire Up, Speed Up i VEST; `K` amaga una clau. VEST dona invulnerabilitat completa durant 17 segons, amb parpelleig i compte enrere al HUD (`VEST 17` fins a `VEST 0`). Un altre VEST reinicia el compte, no l'acumula. No es traspassa entre nivells ni es restaura en carregar-ne un; tampoc evita el game over per temps esgotat. El codi de l'objecte continua sent `Item::BOMB_PROOF`. Les explosions destrueixen l'arbust i s'aturen en aquella casella. L'objecte queda disponible en desaparèixer l'arbust.
- `L` és una porta amb pany: bloqueja moviment i explosions fins que el jugador hi arriba amb la clau; aleshores consumeix la clau, obre el pas i conserva la textura de porta oberta. `D` és la sortida de nivell: s'obre quan no queden enemics. Entrar-hi inicia una animació d'1 segon amb la partida aturada; el nivell es considera superat en acabar-la. Al nivell 5 apareix la pantalla final amb `images/EndScreen.png`. Són portes diferents.
- Un espai és buit; `C` col·loca manualment un núvol decoratiu de **2×1 tiles**, sense col·lisió, dibuixat darrere del mapa. Deixa un espai a la dreta (`C `) per a la segona meitat. No hi ha núvols automàtics; el cel blau cobreix sempre tot el fons. `@` indica l'inici del jugador; `W`, `R`, `Z` i `X` indiquen Walker, Runner, Shooter i boss. `X` marca la cantonada superior esquerra d'un boss de **2×2 tiles**: deixa espai lliure per a tot el seu cos.
- Les files de la graella han de tenir exactament l'amplada declarada, **inclosos els espais finals**; no eliminis espais ni canviïs les dimensions accidentalment. Mantén accessible el recorregut, els enemics i la sortida, i comprova la jugabilitat en modificar un nivell.
- Si l'atles canvia de mida o disposició, revisa les capçaleres dels nivells i també els retalls de píxels de `TileMap.cpp` (arbust i maons), `Item.cpp` i `HUD.cpp` (clau), `Door.cpp` (portes) i `Scene.cpp` (cel i núvols); actualitza la documentació. Les coordenades de textura dels sprites especials són independents de la graella declarada al nivell.

### Boss volador (`X`)

El boss té un cos de 2×2 tiles i 5 vides. Segueix un recorregut creuat de
trams rectes: dalt esquerra → baix dreta → dalt dreta → baix esquerra → inici.
Les caselles de referència són a 4 tiles de cada vora: al mapa de 24×16,
(4,4), (19,11), (19,4) i (4,11). El centre del cos coincideix amb el centre
d'aquestes caselles; la cantonada superior esquerra s'ajusta mig tile amunt
i a l'esquerra. El recorregut és simètric respecte del centre del mapa.
Les `x` minúscules del dibuix no formen part del nivell. S'incorpora a la
cantonada més propera des de `X` sense teletransportar-se. Vola sense gravetat
a 31,5 píxels/segon, ignora els blocs i només el bloquegen les bombes,
incloses les acabades de posar. Si una bomba talla la ruta, espera i la
reprèn quan desapareix. Les plataformes no el transporten.
Manté les ràfegues de 3 boles i s'atura a l'aire durant la preparació,
la recuperació i el dany. Els seus projectils continuen col·lidint amb blocs.
En morir, queda 4 segons immòbil amb la textura de cop i sense fer mal,
amb partícules de 8×8 de `bomberman.png` a (223,540), (233,540), (243,540)
sempre dins del seu cos de 32×32. A partir del segon 2, el cos parpelleja
amb el ritme del jugador, compartit amb `Player::isBlinkVisible`.
Només s'elimina al segon 4 (`isReadyToRemove`), moment en què pot obrir-se
la sortida si no queden altres enemics; també s'aplica amb la tecla `K`.
Vegeu `levels/README.md` per als detalls del recorregut.

### Trampolí (`T`)

El trampolí es posa amb `T` i ocupa una casella. En repòs fa 9 píxels
d'alçada, alineat amb la base del tile; els 7 píxels superiors són buits.
S'activa amb un botó `U` (paret) o `V` (terra) exactament 4 tiles a la dreta
i a la mateixa fila: `T   U` o `T   V`. `V` gira les dues textures 90° i
es dibuixa a la base de la casella, amb terra sòlid a la fila de sota.
No s'activa trepitjant-lo; la resta del comportament és igual que `U`.
Una explosió que arriba al botó, des de qualsevol direcció o al centre,
el comprimeix durant 1 segon i activa el trampolí immediatament.
El botó no té col·lisió, no atura les flames i no es destrueix; els blocs
intermedis sí que poden impedir que l'explosió arribi al botó.
El vincle entre botó i trampolí no necessita un camí lliure.
Sense trampolí al destí, només s'anima el botó. Un botó comprimit ignora
nous impactes fins que es recarrega. Un trampolí actiu no reinicia el cicle.
El trampolí bloqueja les flames i no es destrueix; cap explosió directa l'activa.
L'animació és 9→12→16→12→9 píxels. Es manté completament estès durant 1 segon abans de baixar; el cicle complet dura aproximadament 1,4 segons.
El jugador es manté sobre la superfície i, en arribar als 16 píxels, rep
un impuls de 10 tiles d'alçada des de la superfície elevada, sense prémer salt.
Els sostres aturen l'ascens. Sense jugador a sobre, s'anima igualment.
Un nou impacte mentre està actiu no reinicia l'animació. Els enemics segueixen
la superfície però no reben l'impuls. Deixa espai lliure a sobre per al salt.

#### Desalineament de textures en ampliar l'atles

**Símptoma:** després d'afegir blocs a `personalized.png`, els blocs dels nivells es veuen retallats, deformats o amb parts d'altres textures, tot i que la graella del nivell no ha canviat.

**Causa que ja hem tingut:** l'atles va passar de 48×48 a 48×64 píxels, afegint una fila de tiles de 16×16, però les capçaleres dels nivells encara indicaven `3 3`. `TileMap::prepareArrays` divideix la imatge segons les columnes i files declarades; amb una mida incorrecta, els retalls ja no coincideixen amb els tiles.

**Com solucionar-ho:**

1. Comprova les dimensions reals de la imatge i calcula la graella: amplada / 16 = columnes i alçada / 16 = files. Amb l'atles actual, **80×80 / 16×16 = 5×5**. Els índexs 1–20 es conserven; en passar de 4 a 5 columnes canvien els retalls en píxels, també els dels sprites especials.
2. A cada `levels/levelXX.txt` que utilitzi `images/personalized.png`, actualitza la línia del nombre de tiles del tilesheet perquè indiqui `5 5` (o la graella nova que correspongui). La línia de `16 16` és la mida del tile i del bloc: no és la que indica el nombre de columnes i files.
3. Si només s'han afegit files al final i les peces existents no s'han mogut, conserva els codis de la graella: no cal substituir els blocs. Si ha canviat el nombre de columnes o s'han reordenat peces, recalcula els índexs i adapta els codis necessaris. Preserva les modificacions existents, les dimensions de cada fila i els espais finals.
4. Revisa els retalls de l'arbust, la clau i les portes als fitxers esmentats més amunt: aquests sprites fan servir coordenades pròpies i no depenen de la capçalera del nivell. Actualitza `blocs.md` i `levels/README.md` si cal.
5. Carrega i comprova visualment els nivells afectats: verifica tant els blocs antics com els nous, i també els arbustos i les portes. Si s'ha canviat codi, compila segons les instruccions següents.

## Progrés, ítems i vides entre nivells

- `Game` conserva el progrés durant la sessió, també en tornar al menú. Tancar el joc l'esborra; no hi ha desament en disc.
- Cada nivell guarda els Bomb Up, Fire Up i Speed Up recollits en l'última partida completada, inclosos els recollits amb les millores al màxim. Completar-lo substitueix la seva aportació anterior; abandonar-lo o perdre no la modifica.
- En carregar un nivell, se sumen les aportacions dels nivells anteriors i s'apliquen els límits del jugador. La seva pròpia aportació queda exclosa. Repetir un nivell anterior amb més o menys ítems modifica els valors d'entrada dels posteriors quan es carreguen, conservant les seves aportacions.
- Les vides d'entrada es guarden per separat. Completar un nivell actualitza les del següent, sense recalcular les dels altres. Reintentar restaura les vides d'entrada; el nivell 1 i els nivells sense registre comencen amb 3 vides. Aquestes regles també s'apliquen a les tecles `1`–`5`.
- Les claus, el VEST i la invulnerabilitat de reaparició no es guarden entre càrregues. El selector mostra «START WITH» amb les vides, bombes, foc i velocitat d'entrada, calculats igual que en carregar el nivell. La velocitat base es mostra com a 1 i el màxim és 5.

## Mort i invulnerabilitat del jugador

- En eliminar l'últim enemic del nivell es recupera una vida, fins al màxim de 4, sempre que encara en quedi alguna. Les 3 vides normals són vermelles; la quarta és una vida extra blanca (últim fotograma de la segona fila de `hearts.png`). Només fa blinking durant 1,5 segons en aparèixer o desaparèixer, sense fotogrames de buidatge. Sense aquesta vida, la seva casella no es dibuixa. Es mostra «1 UP» durant els 3 segons de l'avís «EXIT!», també si ja es tenen 4 vides. La recompensa es dona una vegada, quan la llista d'enemics passa a buida; amb el boss espera els 4 segons de mort. No reviu el jugador després d'un game over. La vida blanca es conserva entre nivells i es perd abans de les vermelles.
- Temps límit per nivell: **1 i 5: 3 minuts; 2: 4 minuts; 3 i 4: 5 minuts**. Es defineix a `Game::startLevel` i es passa a `Scene::init` en mil·lisegons.
- El HUD anima els cors perduts durant **1,5 segons**, amb els fotogrames de `hearts.png`: flaix blanc/vermell inicial i parpelleig amb buidatge progressiu fins al cor buit. Per dany normal només s'anima el cor perdut; si s'esgota el temps, s'animen alhora tots els cors restants. Recuperar una vida reprodueix la mateixa animació al revés, de buit a ple. Comparteixen un únic temporitzador de presentació. Carregar el HUD no inicia cap animació; les vides les continua gestionant `Player`.
- En perdre una vida, el jugador reprodueix els quatre fotogrames de mort de `bomberman.png` durant **1,5 segons**, al lloc del cop, sense moure's ni rebre més dany.
- Si queden vides, reapareix a l'inici amb **3 segons d'invulnerabilitat**, comptats des de la reaparició. El parpelleig actual té un cicle de 360 ms: 288 ms visible (80%) i 72 ms invisible (20%).
- El god mode (`G`, toggle) utilitza el mateix parpelleig mentre està activat.
- En perdre l'última vida o esgotar el temps, la pantalla de derrota espera que acabi l'animació. `Player::isAlive()` exclou les animacions de mort i d'entrada a la sortida; `isGameOver()` indica que l'animació de mort ha acabat i no queden vides.

## Compilar i executar

Windows, MSVC, plataforma **Win32**. Des de l'arrel del repo:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .vscode/build.ps1 -Configuration Debug -Platform Win32 -Target Build
```

L'executable surt a `2DGame/02-Bubble/Debug/02-Bubble.exe` i s'ha d'executar amb working directory `2DGame/02-Bubble/02-Bubble/` (així ho fa `.vscode/launch.json`).

La finestra escala el joc mantenint la proporció 4:3, centrat amb franges negres. `F11` alterna pantalla completa al monitor actual i restaura la posició, mida i estat maximitzat de la finestra en sortir. La resolució lògica continua sent 640×480, amb la mateixa càmera i HUD.

Després de qualsevol canvi de codi, compila per comprovar que no hi ha errors.

## Git
- Branca de treball: `develop`; principal: `main`.
- **No facis mai commits ni push si l'usuari no ho demana explícitament.**
- Els fitxers de build (`*.tlog`, `*.idb`, `*.exe`) que surten com a modificats no s'han d'incloure als commits.
