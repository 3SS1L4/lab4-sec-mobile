# Rapport d'Analyse Statique APK - Lab 4
**Cours : Security Mobile**

## 📊 Informations Générales
- **Date d'analyse :** 26 Avril 2026
- **Analyste :** AMSOU ISMAIL (Cyber Defense ENSA)
- **APK analysé :** `UnCrackable-Level1.apk` (ou votre fichier)
- **Hash SHA-256 :** `1da8bf57d266109f9a07c01bf7111a1975ce01f190b9d914bcd3ae3dbef96f21`
- **Provenance :** Lab autorisé / OWASP Crackmes
- **Outils utilisés :** - JADX GUI v1.5.0
    - dex2jar v2.4
    - JD-GUI v1.6.6

---

## 🚀 Résumé Exécutif
Cette analyse statique a porté sur la structure interne, les configurations du manifeste et la recherche de secrets dans l'APK.
- **Vulnérabilités identifiées :** [Nombre, ex: 4]
- **Niveau de risque global :** 🔴 Élevé / 🟠 Moyen
- **Problèmes majeurs :** [Ex: Composants exportés sans protection, Secrets en clair].

### Actions prioritaires recommandées :
1. Désactiver le mode debug dans le manifeste.
2. Obfusquer le code source avec R8/ProGuard.
3. Supprimer les clés API codées en dur.

---

## 🔍 Constats Détaillés

### Constat #1 : Composants Exportés Sensibles
- **Sévérité :** 🔴 Élevée
- **Description :** Une activité (`.MainActivity`) ou un Service est marqué avec `android:exported="true"` sans permission restrictive.
- **Localisation :** `AndroidManifest.xml`
- **Impact potentiel :** Une application malveillante sur le même appareil peut lancer ce composant pour contourner l'authentification.
- **Remédiation :** Définir `exported="false"` si l'accès externe n'est pas nécessaire.

### Constat #2 : Secrets Codés en Dur (Hardcoded Secrets)
- **Sévérité :** 🔴 Élevée
- **Description :** Présence d'une clé secrète ou d'un token utilisé pour une fonction de vérification.
- **Localisation :** `com.example.app.VerifyActivity` (méthode `checkFlag`)
- **Impact potentiel :** Extraction facile du secret par reverse engineering permettant de compromettre la logique métier.
- **Remédiation :** Utiliser Android Keystore pour stocker les secrets ou les déporter côté serveur.

### Constat #3 : Configuration de Débogage Active
- **Sévérité :** 🟠 Moyenne
- **Description :** L'attribut `android:debuggable="true"` est présent.
- **Localisation :** `AndroidManifest.xml`
- **Impact potentiel :** Permet d'attacher un débogueur (comme GDB ou JDWP) au processus en cours pour inspecter la mémoire.
- **Remédiation :** S'assurer que `debuggable` est à `false` pour les builds de production.

---

## 🛠️ Comparaison JADX GUI vs JD-GUI

| Aspect | JADX GUI | JD-GUI |
| :--- | :--- | :--- |
| **Navigation** | Excellente (vue projet Android) | Basique (vue JAR classique) |
| **Ressources** | Décompile le Manifest et XML | Ne lit que le Java |
| **Décompilation** | Très précise, gère bien le Kotlin | Moins performant sur le bytecode moderne |
| **Recherche** | Puissante (Global search) | Limitée à la classe ou au JAR |

---

## 📂 Annexes

### Permissions Demandées
- `android.permission.INTERNET`
- `android.permission.READ_EXTERNAL_STORAGE`

### Composants Critiques Identifiés
- **Activity :** `sg.vantagepoint.uncrackable1.MainActivity` (Exported)
