# Voludi / VoludAI — Esperanto learning app (Delphi / FireMonkey)

Aplicación multiplataforma (Windows, Android) para aprender Esperanto, escrita en
Object Pascal con RAD Studio / Delphi FireMonkey (FMX). Incluye diccionario
("Vortaro"), ejercicios de gramática, juegos de palabras, un asistente de IA
("Babilo"/"MakerAI") con reconocimiento y síntesis de voz, y varios módulos
auxiliares (cubo Rubik, radio, actualizador de la app, etc.).

> Este repositorio no tenía control de versiones hasta ahora. Se inicializó git
> el 2026-09-16 con un commit de snapshot completo antes de cualquier limpieza,
> así que el historial previo del proyecto (tal y como estaba en disco) sigue
> disponible en `git log` / `git show 6e0e91b` si algo se necesita recuperar.

## Variantes del proyecto

El repo contiene **cuatro programas Delphi independientes** que comparten la
mayor parte del código fuente (parecen iteraciones sucesivas del mismo producto,
mantenidas en paralelo en ausencia de un VCS):

| Proyecto (`.dpr`/`.dproj`) | Notas |
|---|---|
| `Voludi2` | Referenciado por `ProjectGroup1.groupproj` → parece ser **la variante activa/actual**. |
| `Voludi` | Iteración anterior a Voludi2. |
| `VoludIA` | Variante centrada en IA. |
| `VoludAI` | Otra variante centrada en IA (nombre muy similar a `VoludIA`, revisar cuál es la vigente). |

Cada variante tiene su propio `.artwork` (`VoludAI.Artwork/`, `VoludIA.Artwork/`,
`Voludi.Artwork/`, `Voludi2.Artwork/`), `.res`, `.deployproj` e `.identcache`.
Otros sub-proyectos independientes menores:

- `AudioRecPlay/` — proyecto de prueba de grabación/reproducción de audio (tiene su propio `readme.md`).
- `web/Projectwebo.dpr` — versión/módulo web (WebModule).
- `vcl/Project3.dpr` — proyecto VCL suelto (Windows clásico, no FireMonkey).

## Estructura de carpetas

```
Forms/            Formularios FMX compartidos (radio, telegram desktop, backend, IA maker...)
IA/               Módulo de inteligencia artificial: chat ("Babilo"), TTS/STT, integración LLM,
                  subcarpetas por proveedor/motor (bass, esperanto, IntentA, MakerAI, oumi, vosk)
datamodules/      TDataModule compartidos (reproductor de radio, juegos orquestados, MakerAI)
data/             Datos de la app: diccionario (word.db), frases/proverbios, Base.db, etc.
ludo/             Juegos tipo "Vaflo"/crucigrama de palabras
wordlines/        Juego "WordLines"
rubik/, *Rubiko*  Módulo del cubo de Rubik (FormRubiko, PronombreRubiko)
examenes/         Exámenes de Esperanto
delphiTraduki/    Traductor simple (SimpleTranslate4D)
telegram/         Integración/cliente de Telegram
Updater/          Auto-actualización de la app (Voludi_AppUpdate)
Test/             Pruebas unitarias (DUnit) de IA/Vosk/Babilo
utils/            Utilidades varias (ChatGPT, WhatsApp, formularios, XML)
vcl/, win/        Código específico de Windows/VCL (SQLite, proyecto VCL suelto)
vosks/            Wrapper de Vosk (reconocimiento de voz offline)
llama/            Integración con modelos Llama
merged/           Binarios nativos de terceros (libvosk.so) y opciones de compilación 32/64 bits
event-bus/        Librería de terceros "Delphi Event Bus" (Apache 2.0, ver event-bus/README.md)
Plutony/          Runtime/soporte de la librería interna "PluTony"
LibrarySupport/, ModelSupport_*/  Carpetas de soporte de paquetes de RAD Studio (generadas por el IDE)
iconos/, images/, sonido/  Recursos gráficos y de audio
setupcustom/, instalacionwin.iss, package/  Instalador Windows (Inno Setup) y empaquetado
web/              Módulo/página web del proyecto
Progs/            (vacía)
```

Los archivos sueltos en la raíz (`Voludi_MainForm.pas`, `Voludi_Tecno*.pas`,
`Vortaro_*.pas`, `FormWindows.pas`, `PronombreRubiko.*`, etc.) son las unidades
"núcleo" de la app — pantallas principales, diccionario, formularios comunes —
compartidas entre las cuatro variantes.

## Limpieza realizada (2026-09-16)

Se inicializó git (`git init` + commit de snapshot) y a continuación se
eliminaron artefactos de compilación regenerables (no se tocó ningún archivo
fuente `.pas`/`.fmx`/`.dpr`):

- **200 archivos `.dcu`** (unidades compiladas)
- Carpetas **`__history/`** y **`__recovery/`** (34 carpetas — backups automáticos del IDE)
- **`.identcache`**, **`.dproj.local`**, **`.groupproj.local`** (caché/estado local del IDE, específico de esta máquina)
- Carpetas de salida de compilación por plataforma: **`Win32/`, `Win64/`, `Android/`, `Android64/`, `bin/`, `dcu/`**

Resultado: el árbol de trabajo pasó de 133 MB / 2149 archivos a **93 MB / 1002
archivos**. Se añadió `.gitignore` para que estos artefactos no se vuelvan a
trackear. Todo lo eliminado sigue disponible en el commit inicial de git por
si hace falta recuperarlo.

## Pendiente de revisión manual (no se ha tocado nada)

Estos elementos parecen duplicados o carpetas vacías, pero podrían ser
intencionados — se dejan listados para que el propietario decida:

- `UIResources.res` vs `UIResources_.res` — **son distintos** (difieren desde el byte 80), no es una copia exacta.
- `Voludi.Artwork/` vs `Voludi.Artwork_/` — carpeta con sufijo `_`, aspecto de backup manual de una versión anterior del artwork.
- `voludi.res` (minúsculas) vs `Voludi.res`/`Voludi2.res`/`VoludAI.res`/`VoludIA.res` — un `.res` por variante, esperado, pero revisar el de minúsculas suelto en la raíz.
- `VoludAI` vs `VoludIA` — nombres casi idénticos, fácil confundirlos; convendría documentar (o renombrar) cuál es la variante vigente.
- `Progs/` y `ModelSupport_LiberVoludi/` — carpetas vacías.
- `voludi32debug` / `voludi64` (sin extensión, en la raíz) — son archivos de datos, no ejecutables ni fuente; propósito no evidente por el nombre.

## Errores estructurales detectados (2026-09-16)

Auditoría de los 4 `.dproj` (`DCCReference` y `DCC_UnitSearchPath`) contra lo
que existe realmente en disco.

**Corregido** (rutas `DCCReference` desactualizadas dentro del repo — el
compilador ya las resolvía vía `DCC_UnitSearchPath`, pero apuntaban a la
ubicación vieja del archivo; ahora apuntan a la real):
- `VoludAI.dproj`: `IA\Voludi_Esperanto.pas` → `IA\esperanto\Voludi_Esperanto.pas`
- `Voludi.dproj`, `VoludIA.dproj`: `IA\Voludi_STT_Vosk_Helper.pas` → `IA\vosk\Voludi_STT_Vosk_Helper.pas`
- `VoludIA.dproj`: `UCommonrubik.pas` → `rubik\UCommonrubik.pas`

**Pendiente / no corregido** (requiere decisión del propietario, no se ha tocado):

- 🔴 **Referencia absoluta rota en los 4 `.dproj`**: `DCCReference Include="E:\TODO2025\FMXRichMemo-main\midemo\Syntax.Code.MarkDown.pas"` — esa ruta no existe en disco. El archivo real está hoy en `E:\D2025\FMXRichMemo\midemo\Syntax.Code.MarkDown.pas` (o una copia vieja en `E:\todo\VISUAL_COMPONENTS\NOTE_MEMO_MD\FMXRichMemo-main\`). Ninguna de las cuatro variantes tiene esa carpeta en su search path, así que probablemente falla al compilar esta unidad en las cuatro.
- 🔴 **`VoludAI.dproj` referencia un archivo que ya no existe ni en el repo ni en su search path**: `Forms\uMakerAi.UI.ChatInputMemo.pas`. El archivo real está fuera del repo en `E:\claudecode\plutony\MakerAI\uMakerAi.UI.ChatInputMemo.pas`. `Voludi2.dproj` sí lo referencia bien (ruta relativa a `..\..\KitMakerAI\`). Indicio de que **`VoludAI` es una variante desactualizada que hoy no compila tal cual está**.
- 🟠 `VoludIA.dproj` referencia `IA\esperanto\Voludi_Esperanto_Grammar.pas`, que **no existe en ningún sitio** del repo ni de las rutas externas conocidas — referencia huérfana.
- 🟠 **Fork de unidad duplicada**: `Vortaro_DataModule.pas` existe con contenido distinto en `datamodules\Vortaro_DataModule.pas` (8.4 KB, usado solo por `VoludIA`) y en `datamodules\neo\Vortaro_DataModule.pas` (5.7 KB, "versión neo", usada por `VoludAI`, `Voludi` y `Voludi2`). No rompe la compilación (cada proyecto apunta a un archivo explícito) pero es código bifurcado: un fix en la versión "neo" no llega a `VoludIA`.
- 🔵 **El repo no es autocontenido**: los 4 proyectos dependen de rutas absolutas fuera de `vasapo` (`E:\D2025\miteruel\PluTony`, `E:\DELPHI13\MakerAi-13`, `E:\D2025\KitMakerAI`, `E:\D2025\todo\bi`, `E:\Python_DELPHI\Python4Delphi-12`, `E:\D2025\fastreport5112`, `E:\todo\whatsapp\...\CrossUrl`, `E:\todo\FastMM4`). Todas existen en esta máquina hoy, pero el repo no compilaría clonado en otra máquina o aislado sin ellas — a diferencia de `event-bus/`, que sí está vendorizado dentro del repo.

### Segunda pasada de auditoría (2026-09-16, más profunda)

- 🔴 **`ProjectGuid` duplicado entre tres proyectos distintos**: `VoludAI.dproj`, `Voludi.dproj` y `Voludi2.dproj` comparten el mismo GUID `{B79EF16E-BFE6-47DF-854E-4F1AE06F1136}` (solo `VoludIA.dproj` tiene uno propio, `{EA335683-...}`). Es casi seguro que viene de clonar el `.dproj` de una variante a la siguiente sin regenerar el GUID. Puede confundir al IDE/MSBuild cuando dos de estos proyectos están abiertos a la vez o en el mismo `.groupproj` (colisión de identidad de proyecto).
- 🔴 **`uses` rotos por renombrado de una dependencia externa (`KitMakerAI`), no solo metadata de `.dproj`**: en el disco, dentro de `E:\D2025\KitMakerAI`, las unidades se renombraron y los prefijos se intercambiaron:
  - `uMakerAi.UI.ChatInputMemo.pas` → ahora es `KitMakerAI\UI\PluTony.UI.ChatInputMemo.pas`
  - `PluTony.Utils.VoiceMonitor.pas` → ahora es `KitMakerAI\uMakerAi.Utils.VoiceMonitor.pas`

  Pero **7 unidades del repo siguen con `uses` a los nombres viejos**: `Forms\uMainChatFull.pas`, `Forms\FormVoludiRadio.pas`, `Forms\Voludi_Form_Vosk_MakerAI.pas`, `Forms\Voludi_Form_Papago_MakerAi.pas`, `IA\vosk\Voludi_STT_Vosk_Windows.pas` (más otras dos ya huérfanas, ver abajo). **`FormVoludiRadio.pas` está incluido en las 4 variantes**, así que este no es un problema solo de `VoludAI`: probablemente rompe la compilación de las cuatro, no solo del `DCCReference` sino del propio código fuente.
- 🟠 **Recuento completo de `DCCReference` rotos por proyecto** (script de auditoría, no solo grep manual): `VoludAI` 2/70, `Voludi` 11/78 (casi todos hacia `PluTony` externo, esperado), `VoludIA` 2/71, **`Voludi2` 27/103** — el proyecto que se asume "activo" es el que más referencias rotas acumula en términos absolutos (aunque la mayoría son hacia el árbol externo `PluTony`, que sí existe pero con nombres de archivo distintos a los que el `.dproj` espera).
- 🟡 **Código fuente huérfano** (no aparece en ningún `DCCReference` de los 4 proyectos principales *ni* en ningún `uses` de ningún otro archivo del repo — candidatos reales a código muerto, a diferencia de la mayoría de "huérfanos de `.dproj`" que sí se usan vía `uses` y solo les falta la entrada en el `.dproj`):
  - `FormRubiko.pas`
  - `Unit1.pas` (60 bytes — parece la unidad en blanco por defecto que crea RAD Studio al pulsar "New Unit")
  - `Voludi_Frame_Gratulon.pas`
  - `Voludi_Tecno_Vosk.pas` (aunque `Voludi_Tecno.pas` y `Voludi_Tecno2.pas`, muy similares, sí se usan)
- ℹ️ De los 152 archivos `.pas` del repo (excluyendo `event-bus/`), 72 no aparecen en el `DCCReference` de los 4 proyectos principales — pero la mayoría pertenece a proyectos satélite propios (`Test/*.dpr` ×4, `vosks/*.dpr` ×3, `web/Projectwebo.dproj`, `vcl/Project3.dproj`, `AudioRecPlay/AudioRecPlay.dproj`) o sí se usan vía `uses` y solo falta añadirlos al `.dproj` (drift de metadata del IDE, no error de compilación).

### Revisión rápida de formularios (`.fmx`) — 2026-09-16

Chequeo superficial (no exhaustivo) de todos los pares `.pas`/`.fmx`/`.dfm`:

- ✅ Todo `.pas` que declara `TForm`/`TFrame`/`TDataModule` tiene su `.fmx`/`.dfm` pareja, y viceversa — sin recursos huérfanos en ningún sentido.
- ✅ El nombre de clase declarado en cada `.pas` coincide con el tipo de objeto del `.fmx` correspondiente — sin desajustes.
- ✅ Todos los `.fmx` están en formato texto válido (`object …`/`inherited …`), sin señales de corrupción o formato binario mezclado.
- 🟡 **4 nombres de clase repetidos en archivos `.pas` distintos**:
  - `TDataModuleVortaro` — el fork ya documentado arriba (`datamodules\Vortaro_DataModule.pas` vs `datamodules\neo\Vortaro_DataModule.pas`).
  - `TForm2` — en `vcl\Unit2.pas` y `web\FormaUnit.pas`; bajo riesgo, son proyectos independientes que nunca compilan juntos, nombre por defecto sin renombrar.
  - `TFormDektopLink` — en `Voludi_Desktop_Links.pas` (raíz) y `Forms\Voludi_Desktop_Telegram.pas`.
  - `TFormTelegram` — en `Voludi_Links.pas` (raíz) y `Forms\Voludi_Telegram.pas`.

  En los dos últimos casos el typo **"Dektop"** (sin la "s") está calcado en ambas copias, lo que sugiere que un archivo se copió del otro en vez de escribirse de cero. Ninguno de los dos pares aparece en el `DCCReference` de ningún `.dproj` (solo se alcanzan vía `uses`), así que no se pudo confirmar con este nivel de revisión si algún proyecto llega a compilar ambas copias del mismo par a la vez — pendiente de mirar si se vuelve a tocar esos formularios.

## Requisitos de compilación

- **RAD Studio / Delphi** (FireMonkey) — el `.dpr`/`.dproj` de la variante que se quiera compilar.
- Dependencias nativas de terceros en `merged/` (p. ej. `libvosk.so` para reconocimiento de voz).
- `event-bus/` es la librería de terceros "Delphi Event Bus" (Apache-2.0, vendorizada dentro del repo).
