# 🚢 ✈️ Position API

Una API REST robusta construida con Node.js y TypeScript para obtener posiciones en tiempo real de barcos y aviones desde múltiples fuentes de datos.

## 🌟 Características

- **🚢 Seguimiento Marítimo**: Posiciones de barcos via AIS (Automatic Identification System)
- **✈️ Seguimiento Aéreo**: Posiciones de aviones via ADS-B (Automatic Dependent Surveillance-Broadcast)
- **🌍 Consultas Geográficas**: Búsqueda por área, proximidad y puertos
- **🕷️ Web Scraping Inteligente**: Utiliza Puppeteer con plugin stealth para evadir detección
- **🔄 Múltiples Fuentes**: Integra MarineTraffic, MyShipTracking y ADS-B Exchange
- **⚡ API Moderna y Legacy**: Endpoints compatibles con versiones anteriores
- **🛡️ Manejo de Errores**: Gestión robusta de fallos y timeouts

## 🚀 Instalación

### Prerrequisitos

- Node.js (v18+ recomendado)
- npm o yarn
- Docker (opcional)

### Instalación Local

```bash
# Clonar el repositorio
git clone https://github.com/transparency-everywhere/position-api.git
cd position-api

# Instalar dependencias
npm install

# Configurar variables de entorno
cp .env.template .env

# Compilar TypeScript
npm run build

# Ejecutar en desarrollo
npm run dev

# Ejecutar en producción
npm start
```

### Instalación con Docker

```bash
# Construir imagen
docker build -t position-api .

# Ejecutar contenedor
docker run -p 5000:5000 position-api

# O usar docker-compose
docker-compose up
```

## ⚙️ Configuración

Edita el archivo `.env` con tus configuraciones:

```env
PORT=5000
PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser
```

## 📖 Uso de la API

### URL Base
```
http://localhost:5000
```

### 🚢 Endpoints de Barcos

#### Obtener Posición Actual por MMSI
```bash
GET /ais/mt/:mmsi/location/latest
```

**Ejemplo:**
```bash
curl "http://localhost:5000/ais/mt/211879870/location/latest"
```

**Respuesta:**
```json
{
  "error": null,
  "data": {
    "timestamp": "2024-01-15T10:30:00.000Z",
    "latitude": 53.551086,
    "longitude": 9.993682,
    "course": 245.5,
    "speed": 12.3,
    "source": "Marinetraffic",
    "source_type": "AIS"
  }
}
```

### ✈️ Endpoints de Aviones

#### Obtener Posición Actual por ICAO
```bash
GET /adsb/adsbe/:icao/location/latest
```

**Ejemplo:**
```bash
curl "http://localhost:5000/adsb/adsbe/abc123/location/latest"
```

**Respuesta:**
```json
{
  "error": null,
  "data": {
    "timestamp": 1705316400,
    "latitude": 52.370216,
    "longitude": 4.895168,
    "course": 180,
    "speed": 450,
    "altitude": 35000,
    "source": "adsbExchange",
    "source_type": "adsb"
  }
}
```

### 🌍 Endpoints Legacy

#### Barcos en Área Geográfica
```bash
GET /legacy/getVesselsInArea/:area
```

**Ejemplo:**
```bash
curl "http://localhost:5000/legacy/getVesselsInArea/WMED,EMED"
```

#### Barcos Cerca de Coordenadas
```bash
GET /legacy/getVesselsNearMe/:lat/:lng/:distance
```

**Ejemplo:**
```bash
curl "http://localhost:5000/legacy/getVesselsNearMe/37.7749/-122.4194/10"
```

#### Barcos en Puerto Específico
```bash
GET /legacy/getVesselsInPort/:shipPort
```

**Ejemplo:**
```bash
curl "http://localhost:5000/legacy/getVesselsInPort/Hamburg"
```

#### Posición Más Reciente (Múltiples Fuentes)
```bash
GET /legacy/getLastPosition/:mmsi
```

**Ejemplo:**
```bash
curl "http://localhost:5000/legacy/getLastPosition/211879870"
```

## 📊 Fuentes de Datos

### 🚢 Marítimas (AIS)
- **MarineTraffic**: Datos primarios via web scraping
- **MyShipTracking**: Fuente de respaldo con parsing HTML

### ✈️ Aéreas (ADS-B)
- **ADS-B Exchange**: API REST para datos de vuelos en tiempo real

## 🗂️ Estructura del Proyecto

```
position-api/
├── src/
│   ├── index.ts                    # Punto de entrada
│   ├── classes/
│   │   ├── server.ts              # Servidor Express
│   │   └── sources/               # Fuentes de datos
│   │       ├── ais/               # Datos marítimos
│   │       │   ├── mt.ts          # MarineTraffic
│   │       │   └── mst.ts         # MyShipTracking
│   │       ├── adsb/              # Datos aéreos
│   │       │   └── adsbe.ts       # ADS-B Exchange
│   │       ├── Position.d.ts      # Definiciones de tipos
│   │       └── Source.ts          # Clase base
│   ├── legacy/                    # API legacy
│   │   ├── api.ts                 # Endpoints legacy
│   │   ├── area.ts                # Consultas geográficas
│   │   └── lib/
│   │       └── puppeteer.ts       # Utilidades de scraping
│   └── static/
│       └── index.html             # Documentación web
├── test/                          # Pruebas
├── docker-compose.yml             # Configuración Docker
├── Dockerfile                     # Imagen Docker
└── package.json                   # Dependencias
```

## 🛠️ Desarrollo

### Scripts Disponibles

```bash
# Desarrollo con recarga automática
npm run dev

# Compilar TypeScript
npm run build

# Ejecutar pruebas
npm test

# Linter
npm run lint
npm run lint:fix

# Formatear código
npm run prettier

# Generar documentación HTML
npm run generate:readme
```

### Ejecutar Pruebas

```bash
# Pruebas unitarias
npm test

# Pruebas de API con curl
npm run test:curl
```

## 🔧 Tecnologías Utilizadas

- **Node.js** - Runtime de JavaScript
- **TypeScript** - Tipado estático
- **Express.js** - Framework web
- **Puppeteer** - Web scraping y automatización
- **node-fetch** - Cliente HTTP
- **Cheerio** - Parsing HTML
- **Jest** - Framework de pruebas
- **Docker** - Contenedorización

## 📝 Ejemplos de Uso

### Integración JavaScript/TypeScript

```typescript
// Obtener posición de barco
const response = await fetch('http://localhost:5000/ais/mt/211879870/location/latest');
const data = await response.json();

if (data.error) {
  console.error('Error:', data.error);
} else {
  console.log('Posición:', data.data);
}
```

### Integración Python

```python
import requests

# Obtener posición de avión
response = requests.get('http://localhost:5000/adsb/adsbe/abc123/location/latest')
data = response.json()

if data['error']:
    print(f"Error: {data['error']}")
else:
    print(f"Posición: {data['data']}")
```

## 🚨 Códigos de Respuesta

| Código | Descripción |
|--------|-------------|
| 200 | Éxito |
| 404 | Vehículo no encontrado |
| 500 | Error interno del servidor |
| 503 | Servicio no disponible |

## 🔍 Casos de Uso

- **Logística Marítima**: Seguimiento de flotas comerciales
- **Control de Tráfico Aéreo**: Monitoreo de vuelos
- **Investigación**: Análisis de patrones de movimiento
- **Aplicaciones Móviles**: Integración en apps de seguimiento
- **Sistemas de Alerta**: Notificaciones basadas en posición

## 🤝 Contribuir

1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia ISC. Ver el archivo [LICENSE](LICENSE) para más detalles.

## 📞 Soporte

- **Issues**: [GitHub Issues](https://github.com/transparency-everywhere/position-api/issues)
- **Documentación**: Visita `http://localhost:5000` después de iniciar el servidor

## 🙏 Agradecimientos

- **MarineTraffic** - Por los datos AIS
- **ADS-B Exchange** - Por los datos de vuelos
- **MyShipTracking** - Por los datos de respaldo
- **Puppeteer Team** - Por la herramienta de scraping

---

**Desarrollado con ❤️ por el equipo de Transparency Everywhere**