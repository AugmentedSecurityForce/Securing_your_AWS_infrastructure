- [OBJET](#objet)
  - [ISO 27001](#iso-27001)
  - [RGPD](#rgpd)
- [DISCLAIMER](#disclaimer)
- [EN CAS DE PROBLEME DE SECURITE](#en-cas-de-probleme-de-securite)
  - [COORDONNEES DE CONTACTS DE VOTRE ENTREPRISE](#coordonnees-de-contacts-de-votre-entreprise)
- [AUTHENTIFICATION](#authentification)
  - [MFA - AUTHENTIFICATION A PLUSIEURS FACTEURS](#mfa---authentification-a-plusieurs-facteurs)
    - [MFA POUR TOUS LES UTILISATEURS DE LA CONSOLE](#mfa-pour-tous-les-utilisateurs-de-la-console)
    - [MFA - COMPTE ROOT](#mfa---compte-root)
    - [ALERTES DE CONNEXION](#alertes-de-connexion)
    - [DETERMINER LE TYPE D'ACCES PAR UTILISATEUR](#determiner-le-type-dacces-par-utilisateur)
- [GESTION DES COMPTES](#gestion-des-comptes)
  - [SUPPRIMER/DESACTIVER LES COMPTES INACTIFS DEPUIS X JOURS](#supprimerdesactiver-les-comptes-inactifs-depuis-x-jours)
  - [POLITIQUE DES MOTS DE PASSE ET ACCESS KEY](#politique-des-mots-de-passe-et-access-key)
  - [NE PAS ACTIVER DE DROITS COMPLETS](#ne-pas-activer-de-droits-complets)
    - [CREER UN ROLE POUR LA GESTION DES INCIDENTS](#creer-un-role-pour-la-gestion-des-incidents)
- [BDD](#bdd)
  - [MISE A JOUR AUTOMATIQUE - INSTANCE RDS](#mise-a-jour-automatique---instance-rds)
  - [DISPONIBILITE PUBLIQUE](#disponibilite-publique)
- [CHIFFREMENT](#chiffrement)
  - [RDS (BASES DE DONNEES)](#rds-bases-de-donnees)
  - [EFS (STOCKAGE DE FICHIER SANS SERVEUR)](#efs-stockage-de-fichier-sans-serveur)
  - [EBS](#ebs)
- [LOGS - AWS CloudTrail](#logs---aws-cloudtrail)
- [ALLER PLUS LOIN](#aller-plus-loin)

# OBJET
Le présent article a pour but de vous aider dans la mise en place des règles à déployer dans votre infrastructure AWS.
Les règles ont pour objectif :
* Rendre votre infrastructure plus sécurisé que par défaut,
* Vous rendre conforme ISO 27001,
* Vous rendre conforme RGPD,
* Ne pas avoir trop d'impact sur la production de vos équipes.

## ISO 27001
La norme ISO 27001 est devenue une référence mondiale pour la sécurité de l'information et est utilisée par de nombreuses organisations pour évaluer et améliorer leur sécurité de l'information. Elle peut également être utilisée comme un moyen de prouver la conformité à des exigences de sécurité spécifiques, telles que les réglementations gouvernementales ou les exigences des clients.

_Attention, ce n'est pas parce qu'une entreprise est certifiée ISO 27001 qu'elle met en place les mesures de sécurité appropriée_

## RGPD
Le RGPD (Règlement général sur la protection des données) est une réglementation européenne qui a pour objectif de renforcer la protection des données personnelles des citoyens de l'Union européenne.

Le rôle du RGPD est de donner aux individus un contrôle plus grand sur leurs données personnelles, ainsi qu'une meilleure visibilité sur la façon dont ces données sont collectées, stockées, utilisées et partagées par les organisations. La réglementation impose également aux organisations de mettre en place des mesures de sécurité techniques et organisationnelles appropriées pour protéger les données personnelles contre les pertes, les vols ou les utilisations non autorisées.

# DISCLAIMER 
Les requêtes sont fournies a titre indicatif, je vous recommande de les tester et les modifier au besoin.

# EN CAS DE PROBLEME DE SECURITE
Les parties suivantes concernent des points de sécurité non liés aux normes citées précédemment.
## COORDONNEES DE CONTACTS DE VOTRE ENTREPRISE
AWS utilise ces coordonnées pour vous contacter dans le cas d'activités suspectes par leurs équipes.

Afin de bien faire : 
* Cette adresse doit être générique (idéalement l'adresse de votre équipe SOC afin qu'une alerte AWS génère une alerte de votre équipe sécurité),
* Le téléphone doit être une ligne toujours disponible (avec un personnel toujours assigné) tel qu'une ligne d'astreinte ou un standard téléphonique.

Pour configurer ces informations suivez cette [documentation AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/manage-account-payment.html).

# AUTHENTIFICATION
## MFA - AUTHENTIFICATION A PLUSIEURS FACTEURS
Le MFA est une méthode d'authentification qui exige la fourniture de deux facteurs d'authentification différents pour accéder à un compte ou à un système. Les facteurs d'authentification couramment utilisés sont généralement un mot de passe et un code généré par une application MFA sur un appareil mobile.

En activant la fonctionnalité MFA sur les comptes AWS, vous augmentez considérablement la sécurité de vos comptes, car l'attaquant aurait également besoin d'un accès physique à l'appareil MFA associé au compte pour se connecter. Cela réduit considérablement les risques de vol de mot de passe et de compromission du compte.
### MFA POUR TOUS LES UTILISATEURS DE LA CONSOLE
Si vous ne configurez pas le MFA sur les comptes ayant accès à la console AWS, les comptes peuvent être vulnérables à des attaques de phishing ou de vol de mot de passe.

Si les attaquants parviennent à obtenir les identifiants de connexion, ils peuvent accéder à la console AWS, où ils peuvent modifier ou supprimer des données, lancer des instances, ouvrir des ports, etc.

### MFA - COMPTE ROOT
Le compte root est le compte d'administration principal sur AWS, qui a un accès complet à tous les services et ressources de l'environnement. 

Si le compte root est compromis, cela peut entraîner des conséquences catastrophiques, notamment la perte de données sensibles, la modification des configurations, l'arrêt des services, etc.

### ALERTES DE CONNEXION
Assurez-vous d'avoir une alerte à chaque connexion d'un compte qui n'utilise pas le MFA.
Assurez-vous d'avoir une alerte à chaque connexion du compte root.

Documentations : 
* https://www.intelligentdiscovery.io/controls/cloudwatch/cloudwatch-alarm-no-mfa
* https://aws.amazon.com/fr/blogs/security/how-to-receive-notifications-when-your-aws-accounts-root-access-keys-are-used/
* https://aws.amazon.com/fr/blogs/mt/monitor-and-notify-on-aws-account-root-user-activity/

### DETERMINER LE TYPE D'ACCES PAR UTILISATEUR
Par défaut, aucune case à cocher n'est sélectionnée dans la console AWS lors de la création d'un nouvel utilisateur IAM. 
Lors de la certification des informations d'identification de l'utilisateur IAM, vous devez déterminer le type d'accès dont il a besoin.

![accestype](Images/accesstype.png)

* Programmatic access : l'utilisateur peut avoir besoin de faire des appels API, d'utiliser la CLI AWS ou d'utiliser les outils pour Windows PowerShell.
* AWS Management Console Access : l'utilisateur doit accéder à la console de gestion AWS.

Documentation : 
* https://learn.digger.dev/misc/aws-keys.html

# GESTION DES COMPTES
## SUPPRIMER/DESACTIVER LES COMPTES INACTIFS DEPUIS X JOURS
Difficile à mettre en place dans le cas d'une prestation pour une entreprise de service numérique (et encore...) il est nécessaire de s'assurer que les comptes présents soient utilisés et utilisables périodiquement.

Un compte sans activité depuis plusieurs semaines / mois, il est nécessaire de se poser quelques questions : 
* Est-ce que l'utilisateur a encore besoin de ce compte ?
* Est-ce que l'utilisateur sait encore se connecter ?
* Est-ce que l'utilisateur s'est déjà connecté ? Si non, voire les questions précédentes.

Requête AWS : 
```
aws iam list-users --output table
```

## POLITIQUE DES MOTS DE PASSE ET ACCESS KEY
Comme pour toutes les infrastructures, il est important de définir une politique de gestion des mots de passe.

Les risques et probabilité d'exploitation n'étant pas les mêmes que pour vos réseaux internes, il est possible qu'elle soit dédiée à votre infrastructure cloud.

Rappelez-vous : 
* Longueurs (max et min) des mots de passe,
* Complexité,
* Réutilisation,
* Durée de vie (max et min).

## NE PAS ACTIVER DE DROITS COMPLETS
Comme à chaque fois, il est recommandé de suivre le principe de moindre privilèges, c'est-à-dire ne fournir que les droits strictement nécessaires à vos utilisateurs.

Fournir des droits trop larges, dans le cas qui nous intéresse les droits maximaux, est le meilleur moyen de mettre en danger votre infrastructure.

Requête AWS : 
```
aws iam list-policies --query 'Policies[?PolicyId!=`{managed-policies-arn}`].{PolicyName:PolicyName,PolicyArn:Arn}' --output json | jq -r '.[] | select(.PolicyName != "AdministratorAccess") | [.PolicyArn, .PolicyName] | @tsv' | while IFS=$'\t' read -r POLICY_ARN POLICY_NAME; do aws iam get-policy-version --policy-arn "$POLICY_ARN" --version-id $(aws iam list-policy-versions --policy-arn "$POLICY_ARN" --query 'Versions[?IsDefaultVersion==`false`]|[0].VersionId' --output text) --query 'PolicyVersion.Document' | jq -r '.Statement[] | select(.Effect == "Allow" and .Action == ["*"] and .Resource == ["*"]) | .PolicyVersion.PolicyName' | while read -r POLICY_STATEMENT_NAME; do echo "$POLICY_ARN,$POLICY_NAME,$POLICY_STATEMENT_NAME"; done; done
```

### CREER UN ROLE POUR LA GESTION DES INCIDENTS
Le cloud hérite des hérésies qu'on trouve déjà dans les infrastructures on-premise.
Ici on a le fameux "j'ai mis le compte administrateur de domaine pour qu'il puisse changer le mot de passe".

C'est pourquoi il est important de créer un rôle pour permettre aux utilisateurs de gérer les incidents avec AWS Support.

Documentation : 
* https://gsl.dome9.com/D9.AWS.IAM.25.html 

# BDD
## MISE A JOUR AUTOMATIQUE - INSTANCE RDS
Amazon Relational Database Service (Amazon RDS) est un ensemble de services gérés qui facilite la configuration, l'utilisation et la mise à l'échelle des bases de données dans le cloud.

Une version mineure du moteur est une mise à jour de la version du moteur de base de données au sein d'une version majeure du moteur. Par exemple, une version majeure du moteur peut porter le numéro 9.6 et les versions mineures les numéros 9.6.11 et 9.6.12.

Il est possible d'activer l'indicateur 'Auto Minor Version upgrade" afin de recevoir automatiquement les mises à jours mineures du moteur SGBD. Voici les deux prérequis : 
* La base de données exécute une version mineure du moteur inférieure à la version préférée.
* La mise à niveau automatique des versions mineures est activée pour la base de données.

_Il est possible de faire la même chose pour la version majeure, cependant cette manipulation peut être dangereuse au sein d'une infrastructure de production._

Documentation : 
* https://docs.aws.amazon.com/fr_fr/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.Upgrading.html

## DISPONIBILITE PUBLIQUE
Ca fait mal de devoir le dire mais assurez-vous bien que vos bases RDS ne sont pas accessibles à tous (désactiver "database Publicly Accessible" et mettez à jour vos groupes VPC).

Requêtes : 
* Lister les groupes de sécurité avec des instances RDS : 
```
aws rds describe-db-instances --db-instance-identifier your-db-instance-name --query "DBInstances[*].VpcSecurityGroups[*].VpcSecurityGroupId" --output text
```

* Supprimer la règle d'accès : 
```
aws ec2 revoke-security-group-ingress --group-id your-security-group-id --protocol tcp --port 3306 --cidr 0.0.0.0/0
```
* Vérifier : 
```
aws ec2 describe-security-groups --group-ids your-security-group-id --query "SecurityGroups[*].IpPermissions[*].IpRanges[*].CidrIp" --output text
```

_D'une manière générale, vérifier si d'autres ports d'administration sont ouverts aux quatre vents._

# CHIFFREMENT
## RDS (BASES DE DONNEES)
Les BDD stockent souvent des données importantes (aussi bien légalement que pour votre entreprise), c'est pourquoi il est important de s'assurer que chaque instance est chiffrée.

Lorsque le chiffrement RDS est activé, les données stockées sur le stockage sous-jacent de l'instance, les sauvegardes automatisées, les répliques en lecture et les instantanés sont tous chiffrés.

Cette option n'est disponible qu'au lancement de l'instance BDD et non après.

Requête : 
```
aws rds describe-db-instances --db-instance-identifier mydb --query "*[].{StorageEncrypted:StorageEncrypted}" --output text
```

Documentation : 
* https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html

## EFS (STOCKAGE DE FICHIER SANS SERVEUR)
Amazon Elastic File System (EFS) croît et rétrécit automatiquement au fur et à mesure que vous ajoutez et supprimez des fichiers, sans qu'il soit nécessaire de les gérer ou de les allouer.

Une des solutions est de monter le nouveau EFS (avec chiffrement) au côté de l'ancien (non chiffré) et utiliser la commande rsync pour copier les données.

Comme pour le chiffrement des BDD, si l'EFS a été créé sans que cette option soit activée, vous devez créer EFS avec la bonne configuration et transférer les données de l'ancien vers le nouveau.

Requêtes : 
* Créer la clef : 
```
aws kms create-key
```
* Configurer l'EFS pour utiliser cette clef : 
```
aws efs put-encryption-config --file-system-id <filesystem-id> --encryption-config "{\"Encrypted\":true,\"KmsKeyId\":\"<kms-key-arn>\"}"
```
* Contrôler : 
```
aws efs describe-file-systems --file-system-id <filesystem-id> --query 'FileSystems[*].Encrypted'
```

Documentation : 
* https://docs.aws.amazon.com/fr_fr/efs/latest/ug/encryption.html

## EBS
Amazon Elastic Block Store (Amazon EBS) est un service de stockage par bloc facile à utiliser, évolutif et haute performance conçu pour Amazon Elastic Compute Cloud (Amazon EC2).

Comme pour les deux parties précédentes, il est très fortement recommandé d'activer le chiffrement sur les volumes EBS.

Requête : 
```
aws --region <region> ec2 enable-ebs-encryption-by-default
```

Documentation : 
* https://docs.aws.amazon.com/fr_fr/AWSEC2/latest/UserGuide/EBSEncryption.html

# LOGS - AWS CloudTrail
CloudTrail et AWS IAM doivent être activés sur toutes les régions.

CloudTrail est un service AWS qui vous aide à assurer l’audit opérationnel et des risques, la gouvernance et la conformité de votre compte AWS. Les actions effectuées par un utilisateur, un rôle ou un service AWS sont enregistrées en tant qu'événements dans CloudTrail. 

AWS IAM Access Analyzer :
 * aide à identifier les ressources de votre organisation et les comptes qui sont partagés avec une entité externe.
* valide les politiques IAM par rapport à la grammaire des politiques et aux meilleures pratiques.
* génère des politiques IAM basées sur l'activité d'accès dans vos journaux AWS CloudTrail.

Requêtes : 
* Vérifier les activations : 
```
aws accessanalyzer list-analyzers --region <region-name>
```

* Activer pour une région : 
```
aws accessanalyzer enable-analyzer --analyzer-name "default" --type ACCOUNT --region <region-name>
``` 

_Il est important de prendre en compte que cette action augmentera la facture. Méfiance donc._

Documentations : 
* https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html
* https://docs.aws.amazon.com/fr_fr/awscloudtrail/latest/userguide/cloudtrail-user-guide.html

# ALLER PLUS LOIN
N'hésitez pas a mettre en place les audits depuis les référentiels CIS (niveau1 et 2) sur vos infrastructure AWS : https://docs.aws.amazon.com/securityhub/latest/userguide/cis-aws-foundations-benchmark.html