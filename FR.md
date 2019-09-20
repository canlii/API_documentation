# Documentation de l'API REST de CanLII

CanLII opère un API qui permet aux développeurs autorisés d'accéder programmatiquement aux métadonnées des documents figurant dans la collection de CanLII.

L'API est en lecture seule et en style d'architecture REST. Tous les paramètres requis doivent être spécifiés dans les URLs qui sont utilisés pour faire les appels à l'API. L'API retourne l'information demandée en format JSON.

Pour demander une clef d'API, prière de transmettre un message avec vos coordonnées à notre [formulaire de commentaires](https://www.canlii.org/fr/feedback/commentaires.html).

## Paramètres fréquemment utilisés

| Paramètre | Description |
|--|--|
| langue |  {en} or {fr}. Ce paramètre détermine la langue dans laquelle l'API présentera les résultats. Par exemple, lorsqu'on cherche à obtenir une liste de texte législatifs en utilisant legislationBrowse (voir ci-dessous), si on utilise "fr" comme paramètre, le nom français des lois sera présenté. À l'inverse, en utilisant "en", on obtient les titres anglais. Ce ne sont pas tous les textes de notre collection qui sont disponibles dans les deux langues, donc en l'absence d'une version dans la langue du paramètre choisi, l'API retournera les résultats dans la seule langue disponible.
| clef |  Votre clef d'API.

## Obtenir une liste des cours et tribunaux dans la collection de CanLII et leur "databaseId" respectif

### Structure de l'URL
    https://api.canlii.org/v1/caseBrowse/{langue}/?api_key={clef}

### Paramètres

Voir "Paramètres fréquemment utilisés" ci-dessus.

### Exemple d'appel

    https://api.canlii.org/v1/caseBrowse/fr/?api_key={clef}

Cet appel retournera:

    {
      "caseDatabases": [
        {
          "databaseId": "nlpc",
          "jurisdiction": "nl",
          "name": "Provincial Court of Newfoundland and Labrador"
        },
        {
          "databaseId": "oncicb",
          "jurisdiction": "on",
          "name": "Commission d’indemnisation des victimes d’actes criminels"
        },
        {
          "databaseId": "capsdpt",
          "jurisdiction": "ca",
          "name": "Tribunal de la protection des fonctionnaires divulgateurs"
        }
        ... // autres bases de données

## Obtenir une liste de décisions provenant d'une même base de donnée de jurisprudence

### Structure de l'URL
    https://api.canlii.org/v1/caseBrowse/{langue}/{databaseId}/?offset={offset}&resultCount={resultCount}

### Paramètres obligatoires

| Paramètre | Description |
|--|--|
| databaseId |   Voir ci-dessus.
| offset | Le nombre de résultats qu'on souhaite "sauter". Lorsqu'on utilise 0 pour ce paramètre par exemple, l'API présentera les plus récentes entrées dans cette base de donnée, sujet aux paramètres optionnels ci-dessous.
| resultCount | Le nombre de résultats souhaités. Maximum de 10 000.

### Paramètres optionnels

| Paramètre | Description |
|--|--|
| [&publishedBefore=date] **ou** [&publishedAfter=date] | La date à laquelle la décision a été diffusée pour la première fois sur CanLII. (Note: Il peut y avoir un certain délai et un "jeu" de 2 jours est recommandé.)
| [&modifiedBefore=date] **ou** [&modifiedAfter=date] | La date à laquelle le contenu a été modifié pour la dernière fois dans CanLII.
| [&changedBefore=date] **ou** [&changedAfter=date] | La date à laquelle les métadonnées au sujet d'une décision ou son contenu ont été modifiés pour la dernière fois dans CanLII.
| [decisionDateBefore=date] **ou** [&decisionDateAfter=date] | La date de la décision.

Notes:

 - La "date" doit être en format AAAA-MM-JJ (ISO-8601)
 - Les conditions sont inclusives, c'est à dire que lorsqu'on indique "publishedAfter=2012-12-12", l'API retournera les décisions publiées le 2012-12-12.

### Exemple d'appel

    https://api.canlii.org/v1/caseBrowse/fr/onca/?offset=0&resultCount=3&publishedBefore=2015-01-01&api_key={clef}

L'API retournera:

    {
      "cases": [
        {
          "databaseId": "onca",
          "caseId": {
            "en": "2014onca925"
          },
          "title": "Ariston Realty Corp. v. Elcarim Inc.",
          "citation": "2014 ONCA 925 (CanLII)"
        },
        {
          "databaseId": "onca",
          "caseId": {
            "en": "2014onca922"
          },
          "title": "Kassburg v. Sun Life Assurance Company of Canada",
          "citation": "2014 ONCA 922 (CanLII)"
        },
        {
          "databaseId": "onca",
          "caseId": {
            "en": "2014onca921"
          },
          "title": "Gutowski v. Clayton",
          "citation": "2014 ONCA 921 (CanLII)"
        }
      ]
    }

## Obtenir les métadonnées d'une décision en particulier
### Structure de l'URL
    https://api.canlii.org/v1/caseBrowse/{langue}/{databaseId}/{caseId}/?api_key={clef}

### Paramètres

| Paramètre | Description |
|--|--|
| databaseId | Voir ci-dessus.
| caseId | L'identifiant unique de la décision, tel que retourné par l'appel précédent. Correspond généralement à la citation CanLII de la décision.

### Exemple d'appel

Pour obtenir les métadonnées de la décision Dunsmuir (*Dunsmuir* c. *Nouveau-Brunswick*, [2008] 1 RCS 190, 2008 CSC 9 (CanLII)), vous feriez:

    https://api.canlii.org/v1/caseBrowse/fr/csc-scc/2008scc9/?api_key={clef}

L'API retournera ceci:
   
    {
      "databaseId": "csc-scc",
      "caseId": "2008scc9",
      "url": "http://canlii.ca/t/1vxsn",
      "title": "Dunsmuir c. Nouveau-Brunswick",
      "citation": "[2008] 1 RCS 190, 2008 CSC 9 (CanLII)",
      "language": "fr",
      "docketNumber": "31459",
      "decisionDate": "2008-03-07",
      "keywords": "équité procédurale — raisonnabilité — arbitre — norme — contrôle judiciaire",
      "concatenatedId": "2008csc-scc9"
    }

## Obtenir la liste des décisions qui citent une décision donnée ou qui sont citées dans une décision donnée (citateur)
### Structure de l'URL

      https://api.canlii.org/v1/caseCitator/{en}/{databaseId}/{caseId}/metadataType?api_key={clef}

### Paramètres

| Paramètre | Description |
|--|--|
| langue | **Pour ce type d'appel, seul "en" fonctionne**.
| databaseId | Voir ci-dessus.|
| caseId | Voir ci-dessus.|
| metadataType | citedCases **ou** citingCases **ou** citedLegislations|

### Exemple d'appel
    https://api.canlii.org/v1/caseCitator/en/qccs/2008qccs2292/citedCases?api_key={clef}

L'API retournera ceci:

    {
      "citedCases": [
        {
          "databaseId": "qccq",
          "caseId": {
            "fr": "2002canlii32322"
          },
          "title": "9044-3422 Québec Inc. c. Cam-spec international Inc.",
          "citation": "2002 CanLII 32322 (QC CQ)"
        },
        {
          "databaseId": "qcca",
          "caseId": {
            "en": "2005qcca304"
          },
          "title": "Association provinciale des retraités d\u0027Hydro-Québec v. Hydro-Québec",
          "citation": "2005 QCCA 304 (CanLII)"
        }
        ... // autres décisions

## Obtenir une liste des bases de données de lois et règlements

### Structure de l'URL

    https://api.canlii.org/v1/legislationBrowse/{langue}/?api_key={clef}


### Paramètres

Voir "Paramètres fréquemment utilisés" ci-dessus.

### Exemple d'appel

    https://api.canlii.org/v1/legislationBrowse/fr/?api_key={clef}

L'API retournera la liste suivante:

    {
      "legislationDatabases": [
        {
          "databaseId": "caa",
          "type": "ANNUAL_STATUTE",
          "jurisdiction": "ca",
          "name": "Lois annuelles du Canada"
        },
        {
          "databaseId": "ska",
          "type": "ANNUAL_STATUTE",
          "jurisdiction": "sk",
          "name": "Lois annuelles de la Saskatchewan"
        },
        {
          "databaseId": "nur",
          "type": "REGULATION",
          "jurisdiction": "nu",
          "name": "Règlements du Nunavut"
        }
        ... // autres bases de données

## Obtenir la liste des lois ou règlements inclus dans une base de donnée précise

### Structure de l'URL

    https://api.canlii.org/v1/legislationBrowse/{langue}/{databaseId}/?api_key={clef}

### Paramètre

| Paramètre | Description |
|--|--|
| databaseId | Le code de la base de donnée pour laquelle vous souhaitez obtenir une liste. Généralement, ceci correspond au code à deux lettres de la province ou du territoire en question, suivi de "s" pour les lois (tiré du "s" dans le mot anglais "statute"), de "r" pour la réglementation, ou de "a" pour les lois annuelles (lorsque disponibles). Ces codes peuvent être obtenus en utilisant le type d'appel précédent.

### Exemple d'appel

    https://api.canlii.org/v1/legislationBrowse/fr/ons/?api_key={clef}
    
L'API retournera:

    {
      "legislations": [
        {
          "databaseId": "ons",
          "legislationId": "rso-1990-c-a1",
          "title": "Abandoned Orchards Act",
          "citation": "RSO 1990, c A.1",
          "type": "STATUTE"
        },
        {
          "databaseId": "ons",
          "legislationId": "rso-1990-c-a2",
          "title": "Absconding Debtors Act",
          "citation": "RSO 1990, c A.2",
          "type": "STATUTE"
        }
        ... // more statutes

## Obtenir les métadonnées d'une loi ou d'un règlement précis

### Structure de l'URL

    https://api.canlii.org/v1/{legislationBrowse}/{langue}/{databaseId}/{legislationId}/?api_key={clef}


### Paramètres

| Paramètre | Description |
|--|--|
| databaseId | Voir ci-dessus. |
| legislationId | Identifiant unique de la loi ou du règlement dont on souhaite obtenir les métadonnées. Cet identifiant peut être obtenu en utilisant le type d'appel précédent. |

### Exemple d'appel

    https://api.canlii.org/v1/legislationBrowse/fr/ons/rso-1990-c-a1/?api_key={clef}

L'API retournera:

    {
          "legislationId": "rso-1990-c-a1",
          "url": "http://canlii.ca/t/q",
          "title": "Abandoned Orchards Act",
          "citation": "RSO 1990, c A.1",
          "type": "STATUTE",
          "language": "en",
          "dateScheme": "ENTRY_INTO_FORCE",
          "startDate": "1990-12-31",
          "endDate": "1997-03-01",
          "repealed": "YES",
          "content": [
            {
              "partId": "1",
              "partName": "Main"
            }
          ]
        }

## Limitations

Comme dans les exemples ci-dessus, tous les appels à l'API doivent être faits à l'aide de connexions HTTPS. HTTP n'est plus supporté.

De plus, les transferts supérieurs à 10 Mo ne sont pas pris en charge par l'API. Dans les rares cas où de telles réponses soient nécessaires, le message d'erreur suivant sera dorénavant retourné:

    {
        "contentLength": 36771905,
        "error": "TOO_LONG",
        "message": "This content is larger than 10MB. Payloads larger than 10MB are not supported by this API. Please download the file in chunks by using RFC-7233 Range request headers."
    }
