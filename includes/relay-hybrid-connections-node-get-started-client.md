### <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása
* Hozzon létre egy `sender.js` nevű JavaScript-fájlt.

### <a name="add-the-relay-npm-package"></a>A Relay NPM-csomag hozzáadása
* Futtassa az `npm install hyco-ws` parancsot a projektmappában lévő Csomópont parancssorból.

### <a name="write-some-code-to-send-messages"></a>Írjon egy kódrészletet üzenetek küldéséhez
1. Adja hozzá a következő `constants` utasítást a `sender.js` fájl elejéhez.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Adja hozzá a következő Relay `constants` elemeket a `sender.js` osztályhoz a Hibrid kapcsolat részleteivel. Cserélje le a zárójelben lévő helyőrzőket a hibrid gyűjtemény létrehozásakor beszerzett megfelelő értékekre.
   
   1. `const ns` – A továbbítási névtér (Használjon FQDN-t – példa: `{namespace}.servicebus.windows.net`)
   2. `const path` – A hibrid kapcsolat neve
   3. `const keyrule` – Az SAS-kulcs neve
   4. `const key` – Az SAS-kulcs értéke
3. Adja a következő kódot a `sender.js` fájlhoz:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    A listener.js fájlnak így kell kinéznie:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

