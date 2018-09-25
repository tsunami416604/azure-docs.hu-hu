---
title: 'Gyors útmutató: Python a QnA Maker API (V4)'
titleSuffix: Azure Cognitive Services
description: Get information és kód minták segítségével gyorsan használatának első lépései a Microsoft Translator Text API a Microsoft Cognitive Services, Azure-ban.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: 5e95bf768cc00935067edd3365cc3d101be6a00a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034741"
---
# <a name="quickstart-for-microsoft-qna-maker-api-with-python"></a>Gyors útmutató: a Microsoft a QnA Maker API a Pythonnal 
<a name="HOLTop"></a>

Ez a cikk bemutatja, hogyan használható a [Microsoft QnA Maker API](../Overview/overview.md) tegye a következőket a Python használatával.

- [Hozzon létre egy új Tudásbázis.](#Create)
- [Frissíti egy meglévő Tudásbázis.](#Update)
- [Egy kérelem létrehozása vagy frissítése a Tudásbázis állapotának beolvasása.](#Status)
- [Egy meglévő Tudásbázis közzététele.](#Publish)
- [Cserélje le egy meglévő Tudásbázis tartalmát.](#Replace)
- [Töltse le a Tudásbázis tartalmát.](#GetQnA)
- [Válaszok a Tudásbázis használva tehet fel kérdéseket.](#GetAnswers)
- [Tudásbázis adatainak beolvasása.](#GetKB)
- [A megadott felhasználóhoz tartozó összes tudásbázisok adatainak beolvasása.](#GetKBsByUser)
- [Törölje a Tudásbázis.](#Delete)
- [Az aktuális végpont kulcsok beolvasása.](#GetKeys)
- [Az aktuális végpont kulcsok újbóli létrehozásához.](#PutKeys)
- [Első word változtatások az aktuális készletét.](#GetAlterations)
- [Cserélje le a word változtatások az aktuális készletét.](#PutAlterations)

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [Python 3.x](https://www.python.org/downloads/) Ez a kód futtatásához.

Egy **Microsoft QnA Maker API-t** tartalmazó [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kell rendelkeznie. Egy fizetős előfizetői kulcsra van szüksége az [Azure-irányítópultról](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Tudásbázis létrehozása

A következő kód egy új tudásbázist hoz létre a [Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) metódussal.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def create_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()
# /knowledgebases/create returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read ()

def check_status (path):
    print ('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read ()

req = {
  "name": "QnA Maker FAQ",
  "qnaList": [
    {
      "id": 0,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    }
  ],
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
  ],
  "files": []
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
operation, result = create_kb (path, content)
print (pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status (path)
    print (pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print ('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True
```

**Tudásbázis válasz létrehozása**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Vissza a tetejére](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Tudásbázis frissítése

A következő kód egy meglévő tudásbázist frissít az [Update](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) metódussal.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def update_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()
# PATCH /knowledgebases returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read ()

def check_status (path):
    print ('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read ()

req = {
  'add': {
    'qnaList': [
      {
        'id': 1,
        'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
        'source': 'Custom Editorial',
        'questions': [
          'How can I change the default message from QnA Maker?'
        ],
        'metadata': []
      }
    ],
    'urls': [
      'https://docs.microsoft.com/en-us/azure/cognitive-services/Emotion/FAQ'
    ]
  },
  'update' : {
    'name' : 'New KB Name'
  },
  'delete': {
    'ids': [
      0
    ]
  }
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
operation, result = update_kb (path, content)
print (pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status (path)
    print (pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print ('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True
```

**Tudásbázis válasz frissítése**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Vissza a tetejére](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>Kérés állapotának lekérése

Az [Operation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) metódussal ellenőrizheti a kérések állapotát egy tudásbázis létrehozásához vagy frissítéséhez. A metódus használatának megismeréséhez tekintse meg a [Create](#Create) vagy az [Update](#Update) metódus mintakódját.

[Vissza a tetejére](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Tudásbázis közzététele

A következő kód egy meglévő tudásbázist tesz közzé a [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) metódussal.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def publish_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = publish_kb (path, '')
print (pretty_print(result))
```

**Tudásbázis válasz közzététele**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "result": "Success."
}
```

[Vissza a tetejére](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Cserélje le a Tudásbázis

A következő kódot váltja fel a körét, használja a megadott Tudásbázis tartalmát a [cseréje](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_publish) metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def replace_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PUT", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

req = {
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my Knowledge Base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ]
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
result = replace_kb (path, content)
print (pretty_print(result))
```

**Cserélje le a Tudásbázis-válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
    "result": "Success."
}
```

[Vissza a tetejére](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Töltse le a Tudásbázis tartalmát

Az alábbi kód letölti a körét, használja a megadott Tudásbázis tartalmát a [töltse le a Tudásbázis](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_download) metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

# Replace this with "test" or "prod".
env = 'test';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/{0}/{1}/qna/'.format(kb, env);

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_qna (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_qna (path)
print (pretty_print(result))
```

**Töltse le a Tudásbázis-válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Vissza a tetejére](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-using-a-knowledge-base"></a>Válaszok a Tudásbázis használva tehet fel kérdéseket

A következő kód lekéri egy kérdést a megadott Tudásbázis használatával, a segítségével választ a **készítése a válaszok** metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
1. Adja hozzá az alábbi kódot.
1. Cserélje le a `host` a webhely nevét, a QnA Maker előfizetéshez tartozó értéket. További információ: [QnA Maker szolgáltatás létrehozása](../How-To/set-up-qnamaker-service-azure.md).
1. Cserélje le a `endpoint_key` az előfizetéshez tartozó érvényes végponti kulcs-érték. Vegye figyelembe, hogy ez nem ugyanaz, mint az előfizetési kulcs. A végpont kulcsok használatával megtekintheti a [végpont kulcsok beolvasása](#GetKeys) metódus.
1. Cserélje le a `kb` értéket választ szeretné lekérdezni a Tudásbázis Azonosítóját. Vegye figyelembe a Tudásbázis kell már közzétett használatával a [közzététel](#Publish) metódust.
1. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# NOTE: Replace this with a valid host name.
host = "ENTER HOST HERE"

# NOTE: Replace this with a valid endpoint key.
# This is not your subscription key.
# To get your endpoint keys, call the GET /endpointkeys method.
endpoint_key = "ENTER KEY HERE"

# NOTE: Replace this with a valid knowledge base ID.
# Make sure you have published the knowledge base with the
# POST /knowledgebases/{knowledge base ID} method.
kb = "ENTER KB ID HERE"

method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer"

question = {
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_answers (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Authorization': 'EndpointKey ' + endpoint_key,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()
    return response.read ()

# Convert the request to a string.
content = json.dumps(question)
result = get_answers (method, content)
print (pretty_print(result))
```

**Válaszok válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Vissza a tetejére](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Tudásbázis adatainak lekérése

Az alábbi kód adatainak beolvasása, a megadott Tudásbázis körét, használja a [részletek a Tudásbázis](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_kb (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method + kb
result = get_kb (path)
print (pretty_print(result))
```

**Tudásbázis részletek válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Vissza a tetejére](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Felhasználó minden tudásbázisok lekérése

A következő kódot egy adott felhasználó összes tudásbázisok olyan adatainak beolvasása használatával a [első felhasználó tudásbázisok](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasesforuser) metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_kbs (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_kbs (path)
print (pretty_print(result))
```

**A felhasználói válaszra tudásbázisok beolvasása**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Vissza a tetejére](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Az alábbi kód törli a megadott alap használatával ismereteket a [Tudásbázis törlése](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_delete) metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def delete_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("DELETE", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = delete_kb (path, '')
print (pretty_print(result))
```

**Tudásbázis válasz törlése**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "result": "Success."
}
```

[Vissza a tetejére](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Végpont-kulcsok beolvasása

A következő kódot lekérdezi az aktuális végpont kulcsok használatával a [végpont kulcsok beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys) metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_keys (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_keys (path)
print (pretty_print(result))
```

**Végpont kulcsok válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Vissza a tetejére](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Végpont kulcsok frissítése

Aktuális végpont kulcsainak, használatával újragenerálása a következő kódot a [végpont kulcsok frissítése](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_refreshendpointkeys) metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with "PrimaryKey" or "SecondaryKey."
key_type = "PrimaryKey";

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def refresh_keys (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + key_type
result = refresh_keys (path, '')
print (pretty_print(result))
```

**Kulcsok válasz végpont frissítése**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Vissza a tetejére](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>A word változásokból beolvasása

A következő kód lekéri a jelenlegi word változtatások, amely használatával a [töltse le a változtatások](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc) metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_alterations (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_alterations (path)
print (pretty_print(result))
```

**A word változásokból válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Vissza a tetejére](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Cserélje le a word-átalakítások

A következő kódot váltja fel az aktuális word változtatások, amely használatával a [cserélje le a változtatások](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) metódust.

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def put_alterations (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PUT", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

req = {
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
result = put_alterations (path, content)
print (pretty_print(result))
```

**Cserélje le a word változásokból válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "result": "Success."
}
```

[Vissza a tetejére](#HOLTop)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

## <a name="see-also"></a>Lásd még 

[A QnA Maker áttekintése](../Overview/overview.md)
