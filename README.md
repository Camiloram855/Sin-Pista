# 🎭 SIN PISTA

> **TODOS SABEN MENOS UNO.**

Party game social de deducción y engaño para **4–8 jugadores** sentados alrededor de una mesa, en una sola habitación. Todos reciben una pista de la respuesta secreta… menos el impostor. Diseñado desde cero para streamers y clips cortos.

- Documento de concepto: [`Docs/Design/Sin_Pista_Documento_de_Concepto.docx`](Docs/Design/Sin_Pista_Documento_de_Concepto.docx)
- Plataforma inicial: **PC / Steam**
- Estado: **pre-producción — MVP en construcción**

---

## 1. Stack tecnológico

| Necesidad | Tecnología | Por qué |
| --- | --- | --- |
| Motor | **Unity 6 LTS** (URP) | Mejor ecosistema para party games 3D multijugador en Steam, C# y mucho soporte |
| Lenguaje | **C#** | Nativo de Unity |
| Multijugador | **Netcode for GameObjects** (el host es uno de los jugadores) | Gratis, sin servidores dedicados, ideal para grupos de amigos |
| Conexión / salas | **Unity Relay + Unity Lobby** | Evita abrir puertos; se une con un código de sala |
| Chat de voz | **Unity Vivox** | Voz por sala integrada con Unity Gaming Services (plan gratuito inicial) |
| Steam | **Facepunch.Steamworks** (fase posterior) | Logros, invitaciones de amigos, Rich Presence |
| Input | **Input System** | Teclado/ratón hoy, mando mañana |
| UI | **uGUI + TextMeshPro** | Maduro y fácil de animar; la UI del juego es simple |
| Cámara | **Cinemachine** | Planos cinematográficos de la mesa (ideal para stream) |
| Textos | **Unity Localization** | Español e inglés desde el día 1 |
| Datos de contenido | **ScriptableObjects** (Addressables cuando pasemos de ~300 ítems) | Las categorías y respuestas son datos, no código |
| Pruebas | **Unity Test Framework** | Lógica del juego testeable sin abrir la escena |

> ⚠️ Estas son propuestas iniciales. Antes de instalar los paquetes de red y voz, confirma versiones y precios vigentes en la documentación de Unity.

---

## 2. Cómo empezar

1. Instala **Unity Hub** y **Unity 6 LTS** (con módulo *Windows Build Support*).
2. Instala **Git** y **Git LFS** (`git lfs install`).
3. Crea un proyecto Unity nuevo con la plantilla **3D (URP)** llamado `SinPista`, y mezcla en él el contenido de este esqueleto (`Assets/`, `Docs/`, `Tools/`, `.gitignore`, `.gitattributes`).
4. En **Window → Package Manager** instala:
   - `Netcode for GameObjects`
   - `Multiplayer Services` (Relay + Lobby)
   - `Vivox`
   - `Input System`
   - `Cinemachine`
   - `TextMeshPro`
   - `Localization`
   - `Test Framework`
5. Vincula el proyecto a Unity Gaming Services (**Project Settings → Services**).
6. Abre `Assets/_Project/Scenes/Bootstrap` y ponla como **primera escena** en Build Settings.
7. Cuando los paquetes estén instalados, añade sus assemblies a las referencias de los `.asmdef` que los usen (`SinPista.Networking` → Netcode/Relay/Lobby, `SinPista.Voice` → Vivox, `SinPista.UI` → TextMeshPro, etc.).

---

## 3. Estructura de carpetas

```
SinPista/
├── .github/workflows/          # CI: tests y builds automáticos
├── Docs/
│   ├── Design/                 # Documento de concepto, reglas, balance de puntos
│   ├── Architecture/           # Decisiones técnicas, diagramas, protocolo de red
│   ├── Content/                # Guías para escribir pistas y respuestas
│   └── Art/                    # Dirección de arte, referencias, paleta
├── Tools/
│   ├── ContentPipeline/        # Scripts para convertir CSV/Excel → ScriptableObjects
│   └── Build/                  # Scripts de build y subida a Steam
└── Assets/
    ├── Plugins/                # Librerías nativas (Steamworks, etc.)
    ├── ThirdParty/             # Assets comprados / de terceros (aislados del código propio)
    └── _Project/               # TODO lo nuestro vive aquí
        ├── Art/
        │   ├── Characters/     # Models · Rigs · Animations · Materials · Textures
        │   ├── Environment/    # Room · Table · Props · Materials · Textures
        │   ├── Lighting/       # Luces, reflection probes, volúmenes de post-proceso
        │   ├── VFX/            # Partículas y efectos (confeti, acusación, revelación)
        │   └── UI/             # Sprites · Fonts · Icons
        ├── Audio/
        │   ├── Music/          # Música de menú y de partida
        │   ├── SFX/            # UI · Roulette · Voting · Reactions
        │   ├── Ambience/       # Ambiente de la habitación
        │   └── Mixers/         # AudioMixers (voz, música, sonidos del juego)
        ├── Content/            # ← Datos del juego (rejugabilidad = contenido)
        │   ├── Categories/
        │   │   └── Sounds/     # Clips (audios) + Data (ScriptableObjects). MVP: solo esta categoría
        │   ├── Config/         # Reglas ajustables: tiempos, puntos, nº de jugadores
        │   └── Localization/   # es · en
        ├── Prefabs/
        │   ├── Characters/
        │   ├── Environment/
        │   ├── UI/
        │   ├── Networking/     # NetworkManager, prefabs de jugador en red
        │   └── Systems/        # Managers y servicios de escena
        ├── Scenes/
        │   ├── Bootstrap/      # Inicializa servicios y carga el menú
        │   ├── MainMenu/
        │   ├── Lobby/          # Sala de espera, código de sala, lista de jugadores
        │   ├── GameRoom/       # La habitación + la mesa donde ocurre todo
        │   └── Sandbox/        # Escenas de prueba de cada dev
        ├── Settings/
        │   ├── Rendering/      # Assets de URP y perfiles de calidad
        │   └── Input/          # Input Actions
        └── Scripts/            # Un assembly (.asmdef) por módulo
            ├── App/            # Punto de arranque: conecta todos los módulos
            ├── Core/           # Services · Events · StateMachine · Utils
            ├── Gameplay/       # Reglas del juego (Rounds · Roles · Content · Turns ·
            │                   #   Roulette · Discussion · Voting · ImpostorGuess ·
            │                   #   Scoring · Results · Rules)
            ├── Networking/     # Session · Lobby · Sync · Messages
            ├── Voice/          # Chat de voz
            ├── Players/        # Avatar · Seating · Emotes
            ├── Presentation/   # Camera · Characters · Audio · Effects
            ├── UI/             # Screens · HUD · Widgets · Theme
            ├── Platform/       # Steam · Settings · SaveData
            └── Tests/          # EditMode · PlayMode
```

---

## 4. Módulos y reglas de dependencia

Cada módulo de `Scripts/` tiene su propio `.asmdef`. Las flechas indican **quién puede usar a quién** (nunca al revés):

```
                      App
        ┌──────┬────────┼─────────┬──────────┐
        ▼      ▼        ▼         ▼          ▼
       UI  Presentation Networking Voice   Platform
        │      │  │        │         │          │
        │      │  └──►Players       │          │
        ▼      ▼        ▼           ▼          ▼
     Gameplay ◄─────────┘         Core ◄───────┘
        │
        ▼
      Core
```

**Reglas de oro**

1. **`Gameplay` no sabe nada de red, UI ni Unity visual.** Es lógica pura de reglas (¿quién es el impostor?, ¿cuántos puntos se dan?). Así se puede probar con tests rápidos.
2. **`Networking` traduce** las reglas de `Gameplay` a mensajes de red; no decide reglas.
3. **`UI` y `Presentation` solo muestran** lo que pasa; escuchan eventos, no controlan la partida.
4. **`App` es el único lugar** donde se conectan todas las piezas.
5. Si un módulo nuevo necesita referenciar a otro "hacia arriba", se replantea el diseño.

---

## 5. Flujo de una ronda (máquina de estados)

Vive en `Scripts/Gameplay/Rounds` y cada estado tiene su carpeta hermana:

```
Setup → ClueDelivery → Roulette → Turns → Discussion → Voting → ImpostorGuess → Results → (siguiente ronda)
```

| Estado | Qué hace | Carpeta |
| --- | --- | --- |
| Setup | Elige categoría, respuesta e impostor | `Content`, `Roles` |
| ClueDelivery | Envía la pista a los normales; al impostor le dice «No tienes pista» | `Content`, `Networking` |
| Roulette | Decide quién empieza (y el orden) | `Roulette` |
| Turns | Una frase por jugador con temporizador | `Turns` |
| Discussion | 20–30 s de acusaciones | `Discussion` |
| Voting | Voto secreto, luego se revela; regla de empate | `Voting` |
| ImpostorGuess | El impostor intenta adivinar (aunque lo hayan descubierto) | `ImpostorGuess` |
| Results | Revela respuesta e impostor, reparte puntos | `Results`, `Scoring` |

> 📝 El documento de concepto lista la adivinanza antes de la votación (sección 5) pero la describe después de ser descubierto (sección 7). Aquí se sigue la sección 7. Como es una máquina de estados, cambiar el orden es tocar una sola línea.

---

## 6. Decisiones técnicas clave

**El secreto nunca viaja a todos.** La respuesta secreta se envía **solo** a los jugadores normales, con un mensaje dirigido a cada cliente. El impostor nunca la recibe hasta la revelación. No se guarda en variables sincronizadas para todos.

**Se sincronizan IDs, no archivos.** Por la red viaja «respuesta #42»; cada cliente ya tiene el banco de sonidos instalado y lo reproduce localmente.

**El host manda.** Quien crea la sala es la autoridad de la partida (temporizadores, votos, puntos). Es suficiente para un juego entre amigos.

**El contenido es data.** Añadir una categoría o 100 sonidos = crear ScriptableObjects, sin tocar código. `Tools/ContentPipeline` permite cargarlos desde una hoja de cálculo.

**Las reglas son configurables.** Tiempos, puntos y número de jugadores están en `Content/Config` para balancear sin recompilar.

**Voz separada del juego.** `Voice` solo maneja el audio de los jugadores (silenciar, volumen, indicador de "está hablando"); el resto del juego no depende de cómo se implemente.

---

## 7. Plan de prototipo → carpetas involucradas

| Etapa | Objetivo | Dónde se trabaja |
| --- | --- | --- |
| 1 | Mesa, habitación y personajes sentados | `Art/`, `Scenes/GameRoom`, `Players/Seating` |
| 2 | Multijugador y conexión | `Networking/`, `Scenes/Lobby` |
| 3 | Sistema de voz | `Voice/` |
| 4 | Selección de respuesta e impostor | `Gameplay/Content`, `Gameplay/Roles` |
| 5 | Reproducción de sonidos | `Content/Categories/Sounds`, `Presentation/Audio` |
| 6 | Ruleta y turnos | `Gameplay/Roulette`, `Gameplay/Turns` |
| 7 | Temporizador y discusión | `Gameplay/Discussion`, `UI/HUD` |
| 8 | Adivinanza y votación | `Gameplay/Voting`, `Gameplay/ImpostorGuess` |
| 9 | Resultados y puntuación | `Gameplay/Scoring`, `Gameplay/Results` |
| 10 | Prueba con 4–8 personas | `Docs/Design` (anotar resultados) |

**Alcance del MVP:** 4 jugadores · 1 habitación · 1 mesa · voz · categoría *Sonidos* (50–100 clips) · 1 impostor normal.
**Fuera del MVP** (no crear carpetas hasta validar): otras categorías, variantes de impostor, eventos de ruleta, cosméticos, modo espectador, integración con Twitch.

---

## 8. Convenciones

**Código**
- Código en inglés, documentación en español.
- Namespaces = nombre del módulo (`SinPista.Gameplay.Voting`).
- Clases en `PascalCase`, campos privados `_camelCase`.
- Un archivo por clase, con el mismo nombre.

**Assets**
- Prefijos: `PF_` prefab · `M_` material · `T_` textura · `SO_` ScriptableObject · `AC_` animator · `SFX_` / `MUS_` audio.
- Nada de assets sueltos en la raíz de `Assets/`: todo dentro de `_Project`.

**Git**
- Ramas: `main` (estable) · `develop` · `feature/<nombre>` · `fix/<nombre>`.
- Commits cortos y claros: `feat: ruleta selecciona primer jugador`.
- Los `.unity`, `.prefab` y `.asset` se editan de a uno por persona para evitar conflictos.
- Imágenes, modelos y audio van por **Git LFS** (ya configurado en `.gitattributes`).

---

## 9. Regla de diseño

> Si una nueva función no mejora **el engaño, la conversación, la deducción o las reacciones** entre jugadores, se cuestiona antes de entrar al proyecto.

La señal de éxito del prototipo no es cuántos sistemas hay, sino si al terminar una partida el grupo dice: **«otra ronda»**.
