# LAB13_SM

## Objectifs pédagogiques

- Installer et configurer Objection sur un environnement Android
- Comprendre le mécanisme de la commande `android root disable`
- Contourner la détection de root d'une application protégée
- Analyser l'efficacité du bypass via l'interface de l'application

## 🧪 Déroulement du laboratoire

### Étape 1 : Lancement d'Objection sur l'application cible

La commande utilisée pour injecter Objection dans l'application est la suivante :

```bash
objection -g owasp.mstg.uncrackable1 explore --startup-command "android root disable"
```

Sortie obtenue :

```
DeprecationWarning: The option 'gadget' is deprecated. Please use '-n' or '--name' instead
DeprecationWarning: The command 'explore' is deprecated. Use 'objection start' instead of 'objection explore'
Running a startup command... android root disable
(agent) Registering job 276938. Name: root-detection-disable

Runtime Mobile Exploration
by: @leonjza from @sensepost

[tab] for command suggestions
owasp.mstg.uncrackable1 (run) on (Android: 11) [usb] #
```

📸 **Capture d'écran 1 : Console Objection après injection réussie**

<img width="673" height="202" alt="1" src="https://github.com/user-attachments/assets/c4b5db00-c4c1-47f6-9800-cc370eac5d94" />

#### Interprétation des warnings :

- Les avertissements indiquent que certaines options sont dépréciées
- Objection fonctionne malgré tout correctement
- Un job "root-detection-disable" a été enregistré avec succès

### Étape 2 : Vérification du bypass sur l'application

Après injection, l'application Uncrackable1 s'affiche normalement :

Interface observée :

```
Uncrackable1

Enter the Secret String    VERIFY

With special thanks to Bernhard Mueller for creating the app. 
Now maintained by the MSTG project. 
Want more? Check the MSTG playground!
```

📸 **Capture d'écran 2 : Application fonctionnant après bypass root**

<img width="167" height="346" alt="2" src="https://github.com/user-attachments/assets/fc42269d-142e-4e8f-961f-e75cc3c6ab41" />

#### Analyse du résultat :

- L'application ne détecte plus l'environnement rooté
- L'interface de saisie du secret est accessible
- Le message "With special thanks" confirme le lancement normal

## ⚙️ Mécanisme technique de `android root disable`

Objection exécute plusieurs hooks Frida pour neutraliser la détection de root :

| Composant intercepté | Action effectuée |
|---|---|
| `android.os.Build.TAGS` | Force la valeur "release-keys" |
| `java.io.File.exists()` | Retourne false pour /system/xbin/su, /system/app/Superuser.apk |
| `Runtime.getRuntime().exec()` | Neutralise l'exécution de commandes su |
| Méthodes RootBeer | Patche isRooted(), checkSuBinary() |
| Accès à /proc/mounts | Masque les montages suspects |

## 📊 Résultats obtenus

| Statut | Sans Objection | Avec Objection |
|---|---|---|
| Détection root | ✅ détectée | ❌ contournée |
| Application accessible | ❌ bloquée | ✅ fonctionnelle |
| Interface utilisateur | message d'erreur | écran normal |

## 🛠️ Commandes utiles Objection

| Commande | Description |
|---|---|
| `android root disable` | Désactive la détection de root |
| `android sslpinning disable` | Désactive le SSL Pinning |
| `android hooking list classes` | Liste les classes disponibles |
| `android hooking watch class <classe>` | Surveille une classe |
| `help` | Aide complète |

## 🔍 Dépannage rencontré

| Problème | Solution appliquée |
|---|---|
| Warnings de dépréciation | Ignorés (compatibilité ascendante) |
| Connexion USB instable | Vérification adb devices |

## ✅ Validation du laboratoire

### Critères de réussite :

- Objection s'est connecté à l'application cible
- La commande `android root disable` a été exécutée
- L'application s'affiche sans message de détection root
- L'interface "Enter the Secret String" est accessible

## 📚 Références

- [Objection GitHub](https://github.com/sensepost/objection)
- [Frida](https://frida.re/)
- [Uncrackable1 (MSTG)](https://github.com/OWASP/owasp-mastg)
