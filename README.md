# LAB-4-Analyse-statique-d-un-APK-avec-JADX-GUI-dex2jar-JD-GUI

## Task 1 — Préparer le workspace et vérifier l'APK 

Objectif : Créer un environnement de travail organisé et vérifier l'intégrité de l'APK.

1-Créez un dossier de travail pour ce lab :

![Import OVA](https://github.com/user-attachments/assets/74f18d3f-9741-401e-abef-3d2d57e1f2d5)

---

2-Copiez l'APK à analyser dans ce dossier.

![Import OVA](https://github.com/user-attachments/assets/6de23a64-a793-4b7a-8255-4916a2cb9765)

---

3-Vérifiez que l'APK est bien une archive ZIP en utilisant le Magic Number:

![Import OVA](https://github.com/user-attachments/assets/22c9181d-3be4-4788-adfe-5710a2ce21fb)

Action : Lecture des 4 premiers octets du fichier en format Hexadécimal.

Commande : Get-Content .\app-debug.apk -TotalCount 4 | Format-Hex

Résultat : Le code obtenu est 50 4B.

Explication : 50 4B correspond aux initiales PK (Phil Katz), le créateur du format ZIP. Cela prouve que l'APK n'est qu'un dossier compressé contenant le code et les ressources de l'application. Sans cette signature, le fichier serait considéré comme corrompu ou d'un autre type.

---

4-Listez le contenu de l'APK :

Dans cette phase, nous n'utilisons pas d'interface graphique. Nous utilisons PowerShell pour interroger directement la structure binaire de l'APK. C'est ce qu'on appelle l'analyse d'inventaire.

![Import OVA](https://github.com/user-attachments/assets/a7c5ea63-f87f-44df-bc08-c317ebee5f1c)

A. Analyse détaillée des commandes

Add-Type -Assembly System.IO.Compression.FileSystem : Charge la bibliothèque .NET dédiée à la gestion des systèmes de fichiers compressés.

## Utilité : Par défaut, PowerShell ne sait pas "lire" l'intérieur d'un ZIP/APK. Cette commande lui donne les outils nécessaires pour manipuler l'archive sans logiciel tiers (type WinRAR).

---

$apk = Join-Path (Get-Location).Path "UnCrackable-Level1.apk : Crée une variable nommée $apk contenant le chemin absolu vers le fichier cible.

## Cela évite les erreurs de frappe et permet de réutiliser le nom du fichier dans toutes les commandes suivantes de manière propre.

---

Test-Path $apk: Vérifie l'existence réelle du fichier à l'emplacement spécifié.

## C'est une étape de contrôle. Si le résultat est True, la voie est libre. Si c'est False, inutile de continuer, il y a une erreur de chemin.

---

5-Calculez le hash de l'APK pour traçabilité :

![Import OVA](https://github.com/user-attachments/assets/5aa6a1d6-a31a-40ac-9ff6-763eaf7e1eba)

6-(Optionnel) Vérifiez la signature de l'APK :

## Task 2 — Extraire/obtenir l'APK 

![Import OVA](https://github.com/user-attachments/assets/459ae766-68e8-41c5-8eb7-0b6b5138ec3a)

## Task 3 — Analyse avec JADX GUI 

1-Lancez JADX GUI :

![Import OVA](https://github.com/user-attachments/assets/af37fc33-867d-4388-a104-d62a928d102d)

## Analysez le manifeste en détail :


D'après l'image du fichier AndroidManifest.xml de l'application Uncrackable Level 1, voici l'analyse détaillée des éléments demandés :

1. Informations d'identification et SDK
   
Package principal : owasp.mstg.uncrackable1

versionName : 1.0

minSdkVersion : 19

targetSdkVersion : 28

#####################################################

2. les permissions demandées (uses-permission)

Aucune.

Le fichier ne contient aucune balise <uses-permission>.

##############################################

3. Liste des composants:

Activity : sg.vantagepoint.uncrackable1.MainActivity

Services, Receivers et Providers : Aucun n'est déclaré.

#################################

4.  les composants avec android:exported="true" ou avec des intent-filter

Un seul composant est concerné :

Composant : sg.vantagepoint.uncrackable1.MainActivity

Raison : Présence d'un <intent-filter> (Action: MAIN, Category: LAUNCHER).

!!! Note : L'attribut android:exported="true" n'est pas écrit explicitement, mais l'activité est exportée par défaut à cause de cet intent-filter.!!!

############################################

5. la présence de android:usesCleartextTraffic="true" ou android:debuggable="true"

Ni android:usesCleartextTraffic ni android:debuggable ne sont définis dans ce fichier.

#######################

Explorez les ressources importantes :

1. Exploration de strings.xml
   
Les chaînes de caractères définies sont les suivantes :

action_settings : Uncrackable1

app_name : Uncrackable1

button_verify : Verify

edit_text : Enter the Secret String

thanks : Remerciements à Bernhard Mueller et mention du projet MSTG.

###########################

2. Autres fichiers de configuration

network_security_config.xml : Non visible dans les onglets ouverts.

styles.xml : Un onglet est présent pour ce fichier, mais son contenu n'est pas affiché sur l'image.

AndroidManifest.xml : Un onglet est présent, correspondant à l'analyse précédente.

## Task 4 — Recherche de chaînes sensibles

![Import OVA](https://github.com/user-attachments/assets/f115d042-cdaa-43b3-8249-747b71765d13)


1. URLs et Endpoints

Valeur trouvée : http://schemas.android.com/apk/res/android

Emplacement : Balise <manifest>, attribut xmlns:android.

Risque : Nul.

Il s'agit de l'espace de noms standard pour Android, indispensable au fonctionnement du fichier XML.

######################################### 
2. Informations d'authentification

Mots-clés (token, key, secret, password) : Aucun trouvé.

Risque : Nul.

Aucune clé d'API ou mot de passe n'est codé en dur dans les attributs du manifeste.

####################################

3. Indicateurs de mode de développement
   
Mots-clés (debuggable, usesCleartextTraffic) : Absents.

Indicateur trouvé : android:allowBackup="true"

Emplacement : Balise <application>.

Risque : Moyen.

Bien que ce ne soit pas un flag de "debug" pur, laisser la sauvegarde activée permet d'extraire les données de l'application via ADB sur un appareil non rooté, facilitant l'analyse locale des fichiers sensibles par un attaquant.

####################################

4. Autres éléments notables
Package : owasp.mstg.uncrackable1.

Composant exposé : MainActivity est accessible via un intent-filter (catégorie LAUNCHER), ce qui est le comportement normal pour l'entrée principale de l'application.


## Task 5 — Convertir DEX → JAR avec dex2jar 

Objectif : Transformer le bytecode Android en format JAR pour une analyse alternative.

1-Extrayez les fichiers DEX de l'APK :

![Import OVA](https://github.com/user-attachments/assets/c157e6f5-77e5-43a6-adc2-27070ea68290)

![Import OVA](https://github.com/user-attachments/assets/c5eb0249-d2a2-412a-9adc-75b9b33dc500)

![Import OVA](https://github.com/user-attachments/assets/2f8e54e8-a34d-48cc-9deb-9f080a26f13a)

########################

2-Vérifiez les fichiers DEX extraits :

![Import OVA](https://github.com/user-attachments/assets/03eb6e4f-ebf1-4a89-8ed3-2a0ff556fbbe)

#######################
3-Convertissez chaque fichier DEX en JAR :

![Import OVA](https://github.com/user-attachments/assets/021a1fa4-7cb4-445d-9150-4a95a2050c87)

#########################

4-En cas de multi-dex, vous pouvez créer un script pour traiter tous les fichiers :

![Import OVA](https://github.com/user-attachments/assets/e1ca8a86-489a-4713-bf5a-24cd8d7422b4)


## Task 6 — Comparaison JADX vs JD-GUI (15-20 min)

Objectif : Comparer les différentes approches de décompilation pour une analyse plus complète.

Comparaison : JADX-GUI vs JD-GUI

![Import OVA](https://github.com/user-attachments/assets/58a45665-5cbd-4a5b-806b-72b827e716c1)

## Task 7 — Rédiger le mini-rapport (20-30 min)

📄 Rapport d’analyse statique – UnCrackable Level 1
Informations générales

Date d’analyse : 23/2/2026

Analyste : Badr Eddine Drider - Othman Ait Lhaj Loutfi

APK analysé : UnCrackable-Level1.apk

Version : 1.0

Provenance : OWASP Mobile Security – Crackmes Android

Outils utilisés :

JADX GUI

dex2jar

JD-GUI

Résumé exécutif

Cette analyse statique a révélé plusieurs faiblesses de sécurité liées à la logique applicative côté client dans l’application UnCrackable Level 1.
Les principales préoccupations concernent :

la présence d’une logique de vérification de mot de passe côté client,

l’absence de protection efficace contre la rétro-ingénierie,

l’exposition d’une activité via un intent-filter.

Le niveau de risque global est évalué comme Moyen.

Actions prioritaires recommandées :

Déplacer la logique sensible (vérification de secret) côté serveur.

Mettre en place une obfuscation renforcée et des mécanismes anti-reverse.

Restreindre les composants exportés inutilement.

Constats détaillés
Constat #1 : Vérification du mot de passe côté client

Sévérité : Élevée
Description :
Le mécanisme de validation du mot de passe est implémenté directement dans le code de l’application. Cette logique peut être identifiée et modifiée via rétro-ingénierie (JADX/JD-GUI).

Localisation :
sg.vantagepoint.uncrackable1.MainActivity

Impact potentiel :
Un attaquant peut contourner la vérification ou forcer l’affichage du message “Correct”, permettant un accès non autorisé.

Remédiation recommandée :
Déplacer la logique de validation côté serveur ou utiliser un mécanisme cryptographique robuste avec stockage sécurisé (Keystore, API distante).

Constat #2 : Protection insuffisante contre la rétro-ingénierie

Sévérité : Moyenne
Description :

Le code est facilement lisible via JADX et JD-GUI. L’obfuscation est faible et permet de comprendre rapidement la logique métier.

Localisation :
Classes Java décompilées (MainActivity et méthodes associées).

Impact potentiel :
Facilite l’analyse, la modification et le contournement des mécanismes de sécurité.

Remédiation recommandée :
Activer une obfuscation forte (R8/ProGuard), utiliser des mécanismes anti-debug et anti-tampering.

Constat #3 : Activité exposée via intent-filter

Sévérité : Faible à Moyenne
Description :
L’activité principale contient un intent-filter (MAIN/LAUNCHER), ce qui la rend accessible depuis l’extérieur.

Localisation :
AndroidManifest.xml – MainActivity

Impact potentiel :
Peut élargir la surface d’attaque si des fonctions sensibles sont accessibles sans contrôle.

Remédiation recommandée :
Définir explicitement android:exported="false" pour les composants non destinés à être accessibles par d’autres applications.

Annexes
Permissions demandées

Aucune permission critique n’est déclarée dans le manifeste.

Composants exportés

sg.vantagepoint.uncrackable1.MainActivity (via intent-filter MAIN/LAUNCHER)

Conclusion

L’application UnCrackable Level 1 présente des faiblesses intentionnelles liées à la logique de sécurité côté client, ce qui est cohérent avec son objectif pédagogique.
L’utilisation combinée de JADX et JD-GUI a permis d’obtenir une vue complète du code et des ressources, confirmant que les mécanismes de protection sont insuffisants face à une analyse statique.

## Task 8 — Nettoyage 
Objectif : Assurer la conformité et la sécurité de votre environnement de travail.

![Import OVA](https://github.com/user-attachments/assets/3ed5fca4-2868-4a56-af5b-90c33509522d)

Organisez les fichiers d'analyse :

![Import OVA](https://github.com/user-attachments/assets/a8c98a86-29d3-4747-add5-9add6a8c6ba0)

Supprimez les artefacts temporaires:

![Import OVA](https://github.com/user-attachments/assets/708bd751-1c57-499a-b4c7-c4ac3750993a)











