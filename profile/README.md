# Vaka — Organización & Equipo

¡Te damos la bienvenida a la organización de **Vakalabs**! 

Vaka es una solución financiera ligera diseñada para organizar gastos compartidos ("hacer una vaca") en Venezuela. Nuestro objetivo es reducir la fricción económica y ofrecer total transparencia entre amigos, familias y comunidades en un contexto multimoneda (USD y VES) con tasas de cambio congeladas por transacción.

Este documento resume la estructura técnica, la arquitectura y las pautas esenciales para colaborar en el ecosistema de Vaka.

---

## 🗺️ Estructura del Ecosistema

El proyecto central está organizado en torno a los siguientes componentes:

```text
.
├── vaka-app/       # Aplicación móvil construida en Expo (React Native) + Expo Router
├── supabase/       # Migraciones SQL de PostgreSQL, RLS, Storage y Edge Functions
├── docs/           # Especificaciones de producto, arquitectura, decisiones y marca
│   ├── producto/   # Matriz de vectores, UX/bocetos, decisiones tecnológicas
│   ├── desarrollo/ # Sprints, walkthrough y registro de errores/soluciones
│   ├── marca/      # Manual de marca, voz del personaje y especificaciones
│   └── diseño/     # Referencias de estilo visual y frontend
└── .agents/        # Reglas normativas de desarrollo y agentes de IA (AGENTS.md)
```

---

## 🛠️ Stack Tecnológico

Las tecnologías centrales han sido seleccionadas por estabilidad, velocidad y robustez técnica:

| Área | Tecnología | Pautas |
|---|---|---|
| **Frontend Móvil** | React Native + Expo (SDK 57) | Expo Router (file-based routing), React Native `StyleSheet` (sin Tailwind) |
| **Lenguaje** | TypeScript | Modo estricto (`strict: true`), cero `any`, uso de `unknown` + type guards |
| **Backend & DB** | Supabase | PostgreSQL, Auth, Row Level Security (RLS) mandatorio, Storage y Edge Functions |
| **Package Manager** | **pnpm** | **Exclusivo y obligatorio**. Prohibido usar `npm`, `npx` o `yarn` |
| **Tasas de Cambio** | CotizaVE + DolarAPI | CotizaVE como fuente primaria y DolarAPI como fallback (solo en Edge Functions) |

---

## 🏛️ Pilares de Arquitectura

El desarrollo sigue estrictamente los principios de **Clean Architecture**:

```text
src/
├── domain/           # Entidades, Value Objects e interfaces de repositorio (sin dependencias externas)
├── application/      # Casos de uso, DTOs y puertos de servicios externos
├── infrastructure/   # Implementaciones concretas (Supabase, adaptadores de tasas, storage)
└── shared/           # Tipos comunes, utilidades y errores base
```

### Reglas de oro:
1. **Dirección de dependencias**: `domain` ← `application` ← `infrastructure`. La capa `domain` nunca conoce detalles de frameworks o bibliotecas externas.
2. **UI limpia**: Las pantallas dentro de `app/` únicamente interactúan con casos de uso (`UseCase`). Cero lógica de negocio en la capa de presentación.
3. **Aislamiento de Supabase**: Supabase solo se consume dentro de `infrastructure/supabase/`. Si el backend cambiara, únicamente se modifica dicha carpeta.
4. **Manejo de Errores con `Result<T>`**: Todo caso de uso retorna `{ success: true, data: T } | { success: false, error: DomainError }`. Prohibido lanzar excepciones (`throw`) genéricas no tipadas.
5. **Tasas congeladas inmutables**: Cada pago congela su tasa (`rate_at_time`, `rate_source`, `rate_fetched_at`). Jamás se recalcula un pago histórico.
6. **Seguridad en Base de Datos**: RLS habilitado en todas las tablas, borrado lógico (`deleted_at`) para datos financieros y anonimización de datos en bajas de usuario (nunca `CASCADE DELETE`).

---

## 🚀 Onboarding Rápido para Desarrolladores

### 1. Configuración de Gentle-AI (Requerido)
El equipo utiliza [Gentle-AI](https://github.com/Gentleman-Programming/gentle-ai) para sincronizar convenciones, Spec-Driven Development (SDD) y memoria entre agentes de desarrollo:

```bash
# 1. Verificar si Gentle-AI está instalado
gentle-ai --version

# 2. Si no está instalado (Linux/macOS)
curl -fsSL https://gentle-ai-install.gentlemanprogramming.com | bash

# 3. Ejecutar el asistente interactivo
gentle-ai

# 4. Sincronizar configuraciones del repo
gentle-ai sync
```

Consulta [`docs/setup/gentle-ai-setup.md`](docs/setup/gentle-ai-setup.md) para más detalles.

### 2. Instalación de dependencias
Clona el repositorio e instala las dependencias utilizando siempre `pnpm`:

```bash
cd vaka-app
pnpm install
```

### 3. Comandos de desarrollo

```bash
# Iniciar servidor de desarrollo de Expo
pnpm start

# Ejecutar chequeo de tipos con TypeScript
pnpm exec tsc --noEmit

# Diagnóstico de configuración y dependencias
pnpm exec expo-doctor
```

---

## 🤝 Cultura y Convenciones

- **Filosofía SDD (Spec-Driven Development)**: Ningún código se escribe sin especificación previa, ronda de preguntas y plan validado.
- **Comunicación con el equipo**:
  - Decisiones de arquitectura, base de datos y seguridad son lideradas por los perfiles técnicos (Jesús y Valeria).
  - Al comunicar avances o dudas a fundadores no técnicos (Ana), utilizamos lenguaje claro, directo y sin tecnicismos innecesarios.
- **Voz del producto y copy neutro**:
  - Prohibido el uso de términos estigmatizantes o peyorativos (nunca usar "moroso" ni etiquetas punitivas).
  - Toda transacción muestra con claridad la tasa de cambio congelada al momento del registro.
  - La subida de comprobantes requiere microtexto informativo sobre privacidad.
- **Convención de Git**:
  - Un commit por paso o tarea completada.
  - Formato: `sprint-N: breve descripción de la tarea` o Conventional Commits estándar.

---

## 📚 Enlaces y Documentación de Referencia

- Reglas completas de desarrollo: `.agents/AGENTS.md`
- Manual de marca y personalidad: `docs/marca/Vaca_Manual_De_Marca.md`
- Matriz de decisiones técnicas: `docs/producto/matriz_decisiones_tecnologicas.md`
- Registro de errores y soluciones: `docs/desarrollo/errores-y-soluciones.md`


<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->
