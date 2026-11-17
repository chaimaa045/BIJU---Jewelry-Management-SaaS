# BIJU — Solution SaaS & Marketplace pour Bijouteries

[![Node.js](https://img.shields.io/badge/Backend-Node.js%2020%20(ESM)-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![Express](https://img.shields.io/badge/Framework-Express%204-000000?logo=express&logoColor=white)](https://expressjs.com/)
[![MongoDB](https://img.shields.io/badge/Database-MongoDB%206%20ReplicaSet-47A248?logo=mongodb&logoColor=white)](https://www.mongodb.com/)
[![React](https://img.shields.io/badge/Dashboard-React%2018%20%2B%20Vite%205-61DAFB?logo=react&logoColor=black)](https://react.dev/)
[![Next.js](https://img.shields.io/badge/Marketplace-Next.js%2016%20(App%20Router)-000000?logo=next.js&logoColor=white)](https://nextjs.org/)
[![OpenAI](https://img.shields.io/badge/AI%20Copilot-GPT--4o%20%2B%20Whisper-412991?logo=openai&logoColor=white)](https://openai.com/)
[![Docker](https://img.shields.io/badge/Deployment-Docker%20%2B%20Nginx-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)

> **BIJU** est un écosystème logiciel verticalisé conçu pour la gestion opérationnelle et la digitalisation des bijouteries et joailleries. La plateforme intègre un **ERP/CRM SaaS multi-tenant**, un **point de vente (POS)**, un **moteur de cotation de l'or en temps réel**, un **copilote conversationnel et vocal (Arabe/Darija et Français)**, ainsi qu'une **Marketplace publique B2C**.

---

## Sommaire

- [1. Présentation de la Solution](#1-présentation-de-la-solution)
- [2. Enjeux Métier et Proposition de Valeur](#2-enjeux-métier-et-proposition-de-valeur)
- [3. Modules Fonctionnels](#3-modules-fonctionnels)
- [4. Architecture Technique et Multi-Tenancy](#4-architecture-technique-et-multi-tenancy)
- [5. Sécurité et Conformité](#5-sécurité-et-conformité)
- [6. Stack Technologique](#6-stack-technologique)
- [7. Accès et Démonstration](#7-accès-et-démonstration)

---

## 1. Présentation de la Solution

L'écosystème BIJU s'articule autour de trois applications interconnectées :

1. **Dashboard SaaS Métier (`app.biju.ma`)**  
   Interface d'administration pour les bijoutiers : gestion des stocks par carat, pesées brutes et nettes, suivi de la façon, crédit client, réparations d'atelier et réassort fournisseurs.
2. **Portail Public & Marketplace (`biju.ma`)**  
   Vitrine B2C développée sous Next.js, optimisée pour le référencement naturel (SEO), permettant aux consommateurs de consulter le cours officiel de l'or, de parcourir les catalogues des artisans et d'initier des échanges directs via WhatsApp.
3. **API REST Centralisée (`app.biju.ma/api/v1`)**  
   Moteur backend haute performance gérant l'isolation des données multi-tenant, les règles de calculs d'orfèvrerie, le streaming IA, les webhooks et l'édition de documents légaux.

---

## 2. Enjeux Métier et Proposition de Valeur

Le secteur de la joaillerie présente des spécificités opérationnelles non couvertes par les ERP généralistes :

- **Volatilité des cours des métaux précieux** : Les prix doivent être actualisés en temps réel selon les carats (18K, 21K, 24K...).
- **Double pesée et dissociation du travail artisanal** : Nécessité de séparer le poids brut, le poids net (hors pierres) et le coût de façonnage (*façon*).
- **Usages commerciaux traditionnels** : Intégration du rachat d'or usagé (*casse d'or*), facilités de paiement échelonnées (*carnet de crédit client*) et canal de vente direct par messagerie instantanée.
- **Accessibilité linguistique** : Interface vocale et textuelle adaptée aux langues de travail locales (Darija marocaine, Arabe standard et Français).

---

## 3. Modules Fonctionnels

### Moteur de Cotation et Tarification Dynamique
- Récupération automatisée des cours mondiaux et locaux (24K, 22K, 21K, 18K, 14K, Argent).
- Mise en cache résiliente (TTL 20 min) avec tolérance de panne multi-fournisseurs.
- Formule de valorisation automatique : `(Poids Net × Cours Carat) + Façon + Marge + Valeur des Pierres`.

### Gestion des Stocks et Atelier
- Traçabilité granulaire : poids brut, tare, déduction des pierres et pureté.
- Gestion des opérations de casse d'or : rachat, pesée de refonte et compensation sur facture.
- Reconnaissance assistée par vision (OCR Google Cloud Vision / OpenAI Vision) pour la numérisation des fiches et poinçons.
- Génération et lecture de codes-barres / QR codes pour inventaire mobile.

### Point de Vente (POS) et Facturation
- Encaissement multi-modalités (espèces, carte bancaire, virement, or en reprise).
- Gestion du crédit client : échéanciers de paiement, suivi des acomptes et notifications d'impayés.
- Génération de factures et reçus vectoriels conformes (`pdf-lib`) avec QR code de traçabilité.
- Transmission instantanée des devis et pièces comptables via WhatsApp et Email.

### Copilote IA Joaillerie (Voice & Text)
- Modèles intégrés : OpenAI GPT-4o (Chat, Tool Calling, Vision) et Whisper-1 (transcription vocale).
- Prise en charge bilingue native (Arabe/Darija et Français).
- Exécution d'actions par appels d'outils (*Function Calling*) : consultation d'état de stock, enregistrement de dépenses, estimations et synthèses d'activité commerciale.

### Atelier SAV et Réparations
- Fiches de réparation avec enregistrement du poids d'or confié et restitué.
- Suivi du cycle de traitement (Reçu, En atelier, Prêt, Remis au client).

### Marketplace et Vitrine Digitale
- Architecture SSR/SSG pour des performances de chargement optimales (Core Web Vitals).
- Protection contre l'extraction non autorisée de données (rate-limiting anti-scraping).
- Tunnel de conversion orienté prise de contact directe avec l'artisan.

---

## 4. Architecture Technique et Multi-Tenancy

```
                       [ Utilisateurs Web & Mobiles ]
                                     │
                                     ▼
                  [ Nginx Reverse Proxy (SSL / HTTP2) ]
                                     │
         ┌───────────────────────────┼───────────────────────────┐
         ▼                           ▼                           ▼
  [ Vitrine B2C ]             [ Client SaaS ]             [ API Backend ]
  Next.js 16 (App Router)     React 18 + Vite (SPA)       Node.js 20 + Express
  Port: 3000                  Port: 80                    Port: 5000
                                                                 │
                                                       ┌─────────┴─────────┐
                                                       ▼                   ▼
                                                [ MongoDB 6 ]       [ Redis Cache ]
                                                ReplicaSet rs0      Rate-limiting
```

### Principes d'Isolation Multi-Tenant
- **Cloisonnement applicatif strict** : Chaque entité de données porte l'identifiant invariant de la boutique (`shopOwnerId`).
- **Validation cryptographique** : Le middleware d'authentification valide le jeton JWT et injecte l'empreinte du tenant dans le contexte de requête.
- **Filtrage automatique** : Toutes les requêtes d'accès aux collections (produits, ventes, clients, écritures) sont bornées par le tenant courant.

---

## 5. Sécurité et Conformité

- **Headers HTTP renforcés** : Configuration stricte de `Helmet` (HSTS `max-age=31536000`, Content Security Policy, `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`).
- **Politique CORS restrictive** : Liste d'origines autorisées sans recours aux jokers génériques.
- **Protection contre les injections** : Neutralisation des injections NoSQL via `express-mongo-sanitize` et validation systématique des schémas d'entrée.
- **Sécurisation des identités** :
  - Hachage des mots de passe par `bcryptjs` (facteur de coût 12).
  - Contrôle de complexité via `zxcvbn` et vérification des bases de fuites de données (k-anonymity HaveIBeenPwned).
  - Chiffrement symétrique **AES-256** pour les données sensibles au repos.
- **Protection anti-abus** : Limitation du débit (*rate limiting*) gérée par Upstash Redis et validation par Cloudflare Turnstile sur les points d'entrée publics.
- **Traçabilité** : Journalisation d'audit des opérations administratives et financières.

---

## 6. Stack Technologique

| Composant | Technologie | Description |
|---|---|---|
| **API Serveur** | Node.js 20 (ESM), Express.js 4 | Architecture RESTful modulaire |
| **Persistance** | MongoDB 6, Mongoose 9 | Configuration en ReplicaSet pour transactions ACID |
| **Cache & Quotas** | Upstash Redis | Gestion distribuée des sessions et limitation de débit |
| **Application Métier** | React 18, Vite 5, TailwindCSS 3 | Dashboard SPA réactif |
| **Gestion d'État Client** | TanStack React Query v5 | Synchronisation asynchrone des données serveur |
| **Portail Public** | Next.js 16 (App Router), React 19, TailwindCSS 4 | Rendu hybride optimisé pour le référencement |
| **Intelligence Artificielle** | OpenAI GPT-4o, Whisper-1 | Traitement automatique du langage et de la voix |
| **Vision & Documents** | Google Cloud Vision, pdf-lib | OCR d'articles et génération de documents vectoriels |
| **Infrastructure** | Docker, Docker Compose, Nginx, Let's Encrypt | Déploiement conteneurisé sur serveur dédié/VPS |

---

## 7. Accès et Démonstration

Le code source et la propriété intellectuelle de ce projet sont protégés sous licence **propriétaire privée**.

Pour organiser une présentation technique ou obtenir un accès de démonstration :

- **Portail public** : [biju.ma](https://biju.ma)
- **Portail applicatif** : [app.biju.ma](https://app.biju.ma)
- **Contact professionnel** : contact@biju.ma
