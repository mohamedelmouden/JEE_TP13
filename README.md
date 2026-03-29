# TP13 – Authentification OAuth2 avec Spring Boot et Google

> **Étudiant :** Mohamed EL MOUDEN  
> **Email :** m.elmouden1908@uca.ac.ma  
> **Établissement :** FSTG 
> **Date :** 29 Mars 2026

---

## Objectif

L'objectif de ce TP est de comprendre comment déléguer l'authentification d'une application Spring Boot à un fournisseur externe (Google) en utilisant le protocole **OAuth2** et la norme **OpenID Connect (OIDC)**.

L'utilisateur ne s'authentifie plus directement auprès de l'application, mais auprès d'un service d'identité de confiance qui délivre :
- un **Access Token** → pour accéder aux ressources protégées
- un **ID Token (JWT)** → pour identifier l'utilisateur (nom, email, photo...)

---

## Compétences développées

- Expliquer les principes et acteurs du protocole OAuth2
- Configurer une application Spring Boot en tant que client OAuth2
- Mettre en place une authentification avec Google
- Extraire et afficher les informations du profil utilisateur à partir du ID Token
- Comprendre le flux complet d'autorisation et les notions de redirection et scopes

---

## Technologies utilisées

| Technologie | Version | Rôle |
|-------------|---------|------|
| Spring Boot | 3.5.14 | Framework principal |
| Spring Security | 6.5.9 | Gestion sécurité et OAuth2 |
| OAuth2 Client | - | Intégration avec Google |
| Thymeleaf | - | Moteur de template HTML |
| Lombok | - | Réduction du code boilerplate |
| Java | 21 | Langage de programmation |
| Maven | - | Gestion des dépendances |

---

## Configuration du projet

### Paramètres

| Paramètre | Valeur |
|-----------|--------|
| Group | `fstgm.elmouden` |
| Artifact | `demonstration` |
| Package | `fstgm.elmouden.demonstration` |
| Java | 21 |
| Build | Maven |
| Port | **8082** |

### `application.properties`

```properties
server.port=8082

spring.security.oauth2.client.registration.google.client-id=<VOTRE_CLIENT_ID>
spring.security.oauth2.client.registration.google.client-secret=<VOTRE_CLIENT_SECRET>
spring.security.oauth2.client.registration.google.scope=openid,profile,email
spring.security.oauth2.client.provider.google.issuer-uri=https://accounts.google.com
```

---

## Flux OAuth2 / OIDC

```
Navigateur          Spring Boot             Google OAuth2
    |                    |                       |
    |-- GET /profile --->|                       |
    |<-- Redirect -------|                       |
    |                    |                       |
    |------- Authentification Google ----------->|
    |<------ Code d'autorisation ---------------|
    |                    |                       |
    |-- code ----------->|                       |
    |                    |-- Échange code ------->|
    |                    |<-- Access Token + -----|
    |                    |    ID Token            |
    |<-- Session + /profile --|                  |
```

---

## Structure du projet

```
src/main/java/fstgm/elmouden/demonstration/
├── config/
│   └── SecurityConfig.java
├── web/
│   └── HomeController.java
└── DemonstrationApplication.java

src/main/resources/
├── templates/
│   ├── index.html
│   └── profile.html
└── application.properties
```








## Attributs extraits du ID Token

| Attribut | Contenu | Affiché |
|----------|---------|---------|
| `name` | Mohamed EL MOUDEN | ✅ |
| `email` | m.elmouden1908@uca.ac.ma | ✅ |
| `picture` | URL photo Google | ✅ |
| `locale` | Langue utilisateur | ✅ |



## Résultats

### Page d'accueil (`/`)


<img width="960" height="540" alt="home" src="https://github.com/user-attachments/assets/787dee5a-51d8-4e71-aaac-5e7bb4cd2f4d" />

### Page de profil (`/profile`)
<img width="960" height="540" alt="profile" src="https://github.com/user-attachments/assets/b08f9d2c-8bff-453c-84e0-0681f1945ff7" />


### Page de déconnexion (`/logout`)
<img width="960" height="540" alt="logout" src="https://github.com/user-attachments/assets/fc3a4907-a7eb-4afa-91f8-18374fe5fa03" />





## Problèmes rencontrés et solutions

| Problème | Cause | Solution |
|----------|-------|----------|
| `Connection refused` Keycloak | Config Keycloak sans Keycloak lancé | Commenter les lignes Keycloak dans `application.properties` |
| `ERR_TOO_MANY_REDIRECTS` | `.loginPage("/login")` crée une boucle | Supprimer `.loginPage()` de `SecurityConfig` |
| Page "Sign in" basique | Mauvais port (8080 au lieu de 8082) | Accéder sur `http://localhost:8082` |

---

## Lancer le projet

```bash
# Cloner le projet
git clone https://github.com/mohamedelmouden/JEE_TP13.git

# Configurer application.properties avec votre Client ID et Secret Google

# Lancer l'application
mvn spring-boot:run

# Accéder à l'application
http://localhost:8082/profile
```



## Configuration Google Cloud Console

1. Créer un projet sur [console.cloud.google.com](https://console.cloud.google.com)
2. Activer **Google Auth Platform**
3. Créer un client OAuth 2.0 → **Application Web**
4. Ajouter l'URI de redirection :
   ```
   http://localhost:8082/login/oauth2/code/google
   ```
5. Copier le **Client ID** et **Client Secret** dans `application.properties`
6. Ajouter votre email comme **utilisateur de test**

---

## Différence OAuth2 vs OpenID Connect

| | OAuth2 | OpenID Connect |
|-|--------|---------------|
| Objectif | Autorisation | Authentification |
| Token | Access Token | ID Token (JWT) |
| Question | "Peut-il accéder ?" | "Qui est-il ?" |
| Scope | `read`, `write` | `openid`, `profile`, `email` |
