# LAB-4-Analyse-statique-d-un-APK-avec-JADX-GUI-dex2jar-JD-GUI

## Task 1 — Préparer le workspace et vérifier l'APK (10 min)

Objectif : Créer un environnement de travail organisé et vérifier l'intégrité de l'APK.

1-Créez un dossier de travail pour ce lab :

![Import OVA](https://github.com/user-attachments/assets/74f18d3f-9741-401e-abef-3d2d57e1f2d5)

2-Copiez l'APK à analyser dans ce dossier.

![Import OVA](https://github.com/user-attachments/assets/6de23a64-a793-4b7a-8255-4916a2cb9765)

3-Vérifiez que l'APK est bien une archive ZIP :

![Import OVA](https://github.com/user-attachments/assets/22c9181d-3be4-4788-adfe-5710a2ce21fb)

4-Listez le contenu de l'APK :

![Import OVA](https://github.com/user-attachments/assets/a7c5ea63-f87f-44df-bc08-c317ebee5f1c)

5-Calculez le hash de l'APK pour traçabilité :

![Import OVA](https://github.com/user-attachments/assets/5aa6a1d6-a31a-40ac-9ff6-763eaf7e1eba)

6-(Optionnel) Vérifiez la signature de l'APK :

## Task 2 — Extraire/obtenir l'APK (5-10 min)

![Import OVA](https://github.com/user-attachments/assets/459ae766-68e8-41c5-8eb7-0b6b5138ec3a)

## Task 3 — Analyse avec JADX GUI (20-30 min)

1-Lancez JADX GUI :

![Import OVA](https://github.com/user-attachments/assets/af37fc33-867d-4388-a104-d62a928d102d)

## Analysez le manifeste en détail :


Gemini a dit
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

## Task 4 — Recherche de chaînes sensibles (15-20 min)

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


## Task 5 — Convertir DEX → JAR avec dex2jar (15-20 min)

Objectif : Transformer le bytecode Android en format JAR pour une analyse alternative.

![Import OVA](https://github.com/user-attachments/assets/c157e6f5-77e5-43a6-adc2-27070ea68290)

![Import OVA](https://github.com/user-attachments/assets/c5eb0249-d2a2-412a-9adc-75b9b33dc500)

