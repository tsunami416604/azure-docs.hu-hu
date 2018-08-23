---
title: Python a rövid útmutató az Azure Cognitive Services, a Microsoft Translator Speech API |} A Microsoft Docs
description: Get information és kód minták segítségével gyorsan használatának első lépései a Microsoft Translator Speech API a Microsoft Cognitive Services, Azure-ban.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jaswel
ms.openlocfilehash: a24ccd456df533542e7cbd2315fc1050cd82afbf
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "41987641"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-python"></a>Gyors útmutató: a Microsoft Translator Speech API a Pythonnal 
<a name="HOLTop"></a>

Ez a cikk bemutatja, hogyan .wav fájl a kimondott szavakat lefordítása a Microsoft Translator Speech API használatával.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [Python 3.x](https://www.python.org/downloads/) Ez a kód futtatásához.

Telepítenie kell a [websocket-client csomagot](https://pypi.python.org/pypi/websocket-client) Pythonhoz készült.

Szüksége lesz egy .wav "speak.wav" ugyanabban a mappában az alábbi kód fordítása futtatható fájlt. Ezt a fájlt .wav standard PCM, 16 bites, 16kHz Monó formátumban kell lennie. 

Rendelkeznie kell egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a **Microsoft Translator Speech API**. Szüksége lesz egy fizetős kulcs a [Azure irányítópultján](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Beszéd fordítása

A következő kódot a speech egyik nyelvről a másikra fordítja le.

1. Hozzon létre egy új Python-projektet a kedvenc IDE-ben.
2. Adja hozzá az alábbi kódot.
3. Cserélje le a `key` az előfizetéshez tartozó érvényes hozzáférési kulcs-érték.
4. Futtassa a programot.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print ("Connecting...")
client.run_forever()
```

**Beszéd válasz fordítása**

Egy sikeres eredménye "speak2.wav" nevű fájl létrehozása. A fájl tartalmazza a fordítás beszélt "speak.wav" szó.

[Vissza a tetejére](#HOLTop)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Translator Speech oktatóanyag](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Lásd még 

[Translator Speech áttekintése](../overview.md)
[API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
