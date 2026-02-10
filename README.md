# Projecte Final Barça
Aplicació web simple amb Apache + PHP, MySQL i Redis en Docker. Inclou un frontend estàtic, una API en PHP amb rutes per articles, estadístiques i autenticació bàsica per sessió.

## Requisits
- Docker i Docker Compose
- Windows amb fitxer `hosts` editat per domini local (opcional)

## Posada en marxa
1. Arrencar els serveis:
   - `docker compose up -d --build`
2. Accedir al frontend:
   - `https://frontend.local:8443` (recomanat) o `https://localhost:8443`
3. Acceptar el certificat auto-signat si el navegador ho demana.

### Entrades al `hosts`

- `127.0.0.1 frontend.local`
- `127.0.0.1 api.local`

## Serveis i URLs

- Frontend: `https://frontend.local:8443`
- API (mateix origen via reescriptures):
  - `https://frontend.local:8443/articles`
  - `https://frontend.local:8443/stats`
  - `https://frontend.local:8443/register`
  - `https://frontend.local:8443/login`
  - `https://frontend.local:8443/logout`
  - `https://frontend.local:8443/me`
- API directa (alternativa): `https://api.local:8443/` (si l’entrada `hosts` existeix)
- phpMyAdmin: `http://localhost:8081`

## Credencials i BD

- Host MySQL: `barca-mysql`
- BD: `barca_db`
- Usuari: `barca_user`
- Contrasenya: `barca_pass`
- Esquema inicial: `mysql/init/01-schema.sql`
- La API crea/migra:
  - Taula `users` amb `password_hash` (únic per `username`)
  - Taula `site_visits` per comptador de visites

## Flux d’autenticació

- La pàgina només mostra articles/estadístiques si hi ha sessió iniciada.
- Formulari de login/registre a la targeta “Autenticació”.
- En registrar mostra missatge verd/vermell amb el resultat.

### Exemples de proves amb curl

- Registrar (formulari):
  - `curl -k https://localhost:8443/register -H "Host: frontend.local" -H "Content-Type: application/x-www-form-urlencoded" -X POST --data "username=culer&password=abc12345"`
- Login (formulari):
  - `curl -k https://localhost:8443/login -H "Host: frontend.local" -H "Content-Type: application/x-www-form-urlencoded" -X POST --data "username=culer&password=abc12345"`
- Publicar article (JSON):
  - Crear `test_payload.json` amb `{ "title": "Prova", "content": "Text de prova" }`
  - `curl -k https://localhost:8443/articles -H "Host: frontend.local" -H "Content-Type: application/json" -X POST --data-binary "@test_payload.json"`
- Llistar articles:
  - `curl -k https://localhost:8443/articles -H "Host: frontend.local"`
- Estadístiques (incrementa visites i retorna valors reals):
  - `curl -k https://localhost:8443/stats -H "Host: frontend.local"`

## Estructura

- `apache/conf/vhosts/frontend.conf`: vhost frontend amb reescriptures d’API a mateix origen
- `apache/sites/frontend/`: HTML, CSS i JS del frontend
- `apache/sites/api/index.php`: Router en PHP i endpoints (`/articles`, `/stats`, `/register`, `/login`, `/logout`, `/me`)
- `mysql/init/01-schema.sql`: esquema inicial de BD
