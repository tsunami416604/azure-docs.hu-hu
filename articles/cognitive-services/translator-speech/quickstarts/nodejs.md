---
title: 'Gyors útmutató: Translator Speech API Node.js'
titlesuffix: Azure Cognitive Services
description: Információ és kódminták segítségével ismerkedhet meg a Translator Speech API használatának alapjaival.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: a88eade0d5fbfe4fd951e3e98c72a1e697099f1a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466319"
---
# <a name="quickstart-translator-speech-api-with-nodejs"></a>Gyors útmutató: Translator Speech API a node.js használatával 
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Ez a cikk bemutatja, hogyan használhatja a Translator Speech API-t egy .wav fájlban elhangzó beszéd lefordításához.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához [Node.js 6](https://nodejs.org/en/download/) szükséges.

Telepítenie kell a [Websocket-csomagot](https://www.npmjs.com/package/websocket) a Node.js-hez.

A „speak.wav” fájlnak ugyanabban a mappában kell lennie, mint az alábbi kódból fordított végrehajtható fájlnak. Ennek a .wav fájlnak standard PCM, 16 bites, 16 kHz-es, mono formátumúnak kell lennie. Ilyen .wav fájlt a [Text to Speech API-tól](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech) szerezhet be.

Rendelkeznie kell egy [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) és a **Microsoft Translator Speech API-val**. Egy fizetős előfizetői kulcsra van szüksége az [Azure-irányítópultról](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Beszéd fordítása

A következő kód beszédet fordít le egyik nyelvről a másikra.

1. Hozzon létre egy új Node.js-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Text to Speech API. See:
https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {
    
    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio file is finished.
At 32 bytes per millisecond, this is 100 seconds of silence. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });
                            
    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);
        
        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });
        
        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**Beszéd fordításának válasza**

A „speak2.wav” fájl létrejötte sikeres eredményt jelent. Ez a fájl tartalmazza a „speak.wav” fájlban elhangzó szavak fordítását.

[Vissza a tetejére](#HOLTop)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Translator Speech – oktatóanyag](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Lásd még 

[A Translator Speech áttekintése](../overview.md)
[API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
