# TryHackMe-Investigating Windows

**Introduction:**
Une machine Windows a été piratée et votre travail consiste à enquêter sur la machine Windows et à trouver des indices sur ce que le pirate informatique a pu faire.
**Le défi commence :**
Dès que je me suis connecté, j'ai trouvé un programme en cours d'exécution.
![请添加图片描述](https://img-blog.csdnimg.cn/direct/9b38957d9a874354851d97fe2883b195.png)
C'est un programme qui automatise le processus.
Trouvez d’abord la réponse en question
### 1.Whats the version and year of the windows machine?
Appuyez sur "Fenêtre+R", entrez **cmd** pour ouvrir la ligne de commande, entrez **systeminfo**
Vous pouvez dire qu'il s'agit d'un **Windows Server 2016**
![请添加图片描述](https://img-blog.csdnimg.cn/direct/932fc47eb79148dab50679bd12981564.png)
### Which user logged in last?
Appuyez sur les touches `Windows + R` pour ouvrir la boîte de dialogue Exécuter.
 Tapez `eventvwr.msc` et appuyez sur `Entrée`.
 - Dans le panneau de gauche, cliquez sur **Journaux Windows**.
 - Sélectionnez **Sécurité** pour afficher les journaux de sécurité.
 - ![请添加图片描述](https://img-blog.csdnimg.cn/direct/aecff0acf9a840e6836338ca14add7e3.png)
Puis filtrez à droite
![请添加图片描述](https://img-blog.csdnimg.cn/direct/09d2458d7cb74fde8004b22ed2ee52bd.png)
Les utilisateurs de la vue peuvent filtrer 4624 ou 4648
C'est une capture parfaite de votre connexion, votre premier choix 4624, votre première priorité 4648.
C’est l’endroit idéal pour séjourner 4648, c’est l’endroit idéal pour démarrer votre connexion C’est la meilleure façon de gérer votre administrateur.
![请添加图片描述](https://img-blog.csdnimg.cn/direct/4553ee1cd38c48abab4a1adbc5c764f0.png)
Faites défiler vers le bas pour voir quel utilisateur s'est connecté la dernière fois
![请添加图片描述](https://img-blog.csdnimg.cn/direct/315bd8a3cdd34dda888074bf0ccb0bcb.png)
Vous pouvez voir qu'il est également l'utilisateur administrateur. La date est un peu anticipée. Cela devrait être l'heure à laquelle le drone cible a été créé.

### When did John log onto the system last?
Il existe deux méthodes de requête ici. Comme je ne me suis pas connecté au compte de John, la dernière heure de connexion est la dernière heure de connexion.
Utilisez directement l'utilisateur Internet John pour voir
![请添加图片描述](https://img-blog.csdnimg.cn/direct/446b64a86d3f405eba638c9950f62115.png)
Vous pouvez également le vérifier dans le journal
![请添加图片描述](https://img-blog.csdnimg.cn/direct/13a9db3369b349528dab3d9b72d5c2ed.png)
### What IP does the system connect to when it first starts?
Ce problème a été mentionné ci-dessus. Un programme a démarré automatiquement après le démarrage.
![请添加图片描述](https://img-blog.csdnimg.cn/direct/15dba2268c1843a1ae09d3421ca1b50f.png)
### What two accounts had administrative privileges (other than the Administrator user)?

```bash
net localgroup administrators
```
![请添加图片描述](https://img-blog.csdnimg.cn/direct/81ed2b6c3ae94baf95579edf3995ddcf.png)
### Whats the name of the scheduled task that is malicous.
Pendant le processus de réponse aux questions, une tâche ne cessait de démarrer - je me souviens que le nom du programme est **C:\TMP\mim.exe**
Appuyez sur **"Fenêtre+R"** et entrez **taskchd.msc** pour ouvrir la tâche planifiée
Vous pouvez voir que le mim.exe qui vient d'être enregistré est exécuté toutes les cinq minutes. En regardant le code exécuté, il est considéré comme étant **minikatz**. Le mot de passe système est lu toutes les cinq minutes et enregistré dans un fichier.

![请添加图片描述](https://img-blog.csdnimg.cn/direct/17569b7db32b4d5497ac0fc0d115f15b.png)
Mais la réponse à cette question est une autre tâche planifiée, utilisant **nc.ps1** pour ouvrir un port d'écoute
![请添加图片描述](https://img-blog.csdnimg.cn/direct/0e9315bb815d4a8b93d85c489ade3f64.png)
### What file was the task trying to run daily?
**nc.ps1**  dans question 6

### What port did this file listen locally for?
**1348**  dans question 6

### When did Jenny last logon?
`net user jenny` On peut constater qu'elle ne s'est jamais connectée
![请添加图片描述](https://img-blog.csdnimg.cn/direct/942972875adc4c4c992327b60d430fef.png)
### At what date did the compromise take place?
Heure de création du compte Jenny
La réponse est donc le 02/03/2019

### During the compromise, at what time did Windows first assign special privileges to a new logon?
Vous devez revenir à l'observateur d'événements ou filtrer le journal de sécurité 4648 et faire défiler vers le bas pour voir l'heure de la première connexion de l'utilisateur.

![请添加图片描述](https://img-blog.csdnimg.cn/direct/3c078f27dcf442259fa2546295934d3b.png)
### What tool was used to get Windows passwords?
**mimikatz** dans question 6

### What was the attackers external control and command servers IP?
Lors de la recherche de cette question, j'ai d'abord recherché plusieurs adresses IP d'accès aux journaux de connexion à distance avec des heures de tâches planifiées similaires, mais aucune d'entre elles n'était correcte.
J'ai trouvé la bonne réponse en voyant la question suivante
### What was the extension name of the shell uploaded via the servers website?
Vérifiez le dossier du site Web par défaut
![请添加图片描述](https://img-blog.csdnimg.cn/direct/54d9d34aa97f40d6b8befb6169af3936.png)

### What was the last port the attacker opened?
Vérifiez ce pare-feu
![请添加图片描述](https://img-blog.csdnimg.cn/direct/ce45ed0a7a7c42e8addd529815a462cf.png)
### Check for DNS poisoning, what site was targeted?
Vérifiez cette analyse d'hôte
emplacement du fichier:
**C:\Windows\System32\drivers\etc\hosts**
Vous pouvez voir que Google pointe vers une IP
![请添加图片描述](https://img-blog.csdnimg.cn/direct/2908ab31f8eb46839593138c7dee9527.png)

