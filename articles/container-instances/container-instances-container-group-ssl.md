---
title: SSL engedélyezése oldalkocsis tárolóval
description: Hozzon létre egy SSL- vagy TLS-végpontot az Azure Container-példányokban futó tárolócsoporthoz a Nginx oldalkocsis tárolóban futtatásával
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 43b39c7c13d6d5e52aae2ce1706e4880ab27d225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294944"
---
# <a name="enable-an-ssl-endpoint-in-a-sidecar-container"></a>Ssl-végpont engedélyezése oldalkocsitárolóban

Ez a cikk bemutatja, hogyan hozhat létre egy [tárolócsoportot](container-instances-container-groups.md) egy alkalmazástárolóval és egy SSL-szolgáltatót futtató oldalkocsitárolóval. Ha egy tárolócsoportot külön SSL-végponttal állít be, az alkalmazáskód módosítása nélkül engedélyezheti az SSL-kapcsolatokat az alkalmazásszámára.

Egy két tárolóból álló példatárolócsoportot állít be:
* Egy alkalmazástároló, amely egy egyszerű webalkalmazást futtat a nyilvános Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) lemezkép használatával. 
* A nyilvános [Nginx-lemezképet](https://hub.docker.com/_/nginx) futtató oldalkocsis tároló, amely SSL használatára van konfigurálva. 

Ebben a példában a tárolócsoport csak a nginx 443-as portját teszi elérhetővé nyilvános IP-címmel. A Nginx a HTTPS-kérelmeket a társwebalkalmazáshoz irányítja, amely a 80-as porton belül figyel. Módosíthatja a példát a más portokon figyelő tárolóalkalmazásokhoz. 

Lásd: [További lépések](#next-steps) az SSL tárolócsoportban való engedélyezésének további lépéseiért.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését a cikk befejezéséhez. Ha helyileg szeretné használni, a 2.0.55-ös vagy újabb verzió használata ajánlott. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

A Nginx SSL-szolgáltatóként való beállításához SSL-tanúsítványra van szükség. Ez a cikk bemutatja, hogyan hozhat létre és állíthat be önaláírt SSL-tanúsítványt. Éles környezetben tanúsítványt kell beszereznie egy hitelesítésszolgáltatótól.

Önaláírt SSL-tanúsítvány létrehozásához használja az Azure Cloud Shellben elérhető [OpenSSL](https://www.openssl.org/) eszközt és számos Linux-disztribúciót, vagy használjon hasonló ügyféleszközt az operációs rendszerben.

Először hozzon létre egy tanúsítványkérelmet (.csr fájlt) a helyi munkakönyvtárban:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Az azonosító adatok hozzáadásához kövesse az utasításokat. A Köznapi név mezőbe írja be a tanúsítványhoz társított állomásnevet. Amikor a rendszer jelszót kér, a jelszó hozzáadásának kihagyása érdekében nyomja le az Enter billentyűt gépelés nélkül.

Futtassa a következő parancsot az önaláírt tanúsítvány (.crt fájl) létrehozásához a tanúsítványkérelemből. Példa:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Most három fájlt kell látnia a címtárban: a tanúsítványkérelmet (`ssl.csr`), a személyes kulcsot (`ssl.key`), és az önaláírt tanúsítványt (`ssl.crt`). Használja, `ssl.key` `ssl.crt` és a későbbi lépésekben.

## <a name="configure-nginx-to-use-ssl"></a>A Nginx beállítása az SSL használatára

### <a name="create-nginx-configuration-file"></a>Nginx konfigurációs fájl létrehozása

Ebben a szakaszban hozzon létre egy konfigurációs fájlt nginx ssl használatára. Először másolja a következő szöveget `nginx.conf`egy új fájlba, melynek neve . Az Azure Cloud Shellben a Visual Studio-kód segítségével hozhatja létre a fájlt a munkakönyvtárában:

```console
code nginx.conf
```

A `location`alkalmazásban ügyeljen `proxy_pass` arra, hogy az alkalmazásnak megfelelő porttal állítsa be. Ebben a példában a 80-as portot állítottuk be a `aci-helloworld` tárolóhoz.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-kódolja a titkos kulcsokat és a konfigurációs fájlt

Base64-kódolja a Nginx konfigurációs fájlt, az SSL tanúsítványt és az SSL-kulcsot. A következő szakaszban adja meg a kódolt tartalom egy YAML-fájlban a tárolócsoport üzembe helyezéséhez használt.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Tárolócsoport telepítése

Most telepítse a tárolócsoportot a Tárolókonfigurációk megadásával egy [YAML-fájlban.](container-instances-multi-container-yaml.md)

### <a name="create-yaml-file"></a>YAML-fájl létrehozása

Másolja a következő YAML-fájlt `deploy-aci.yaml`egy új fájlba, melynek neve . Az Azure Cloud Shellben a Visual Studio-kód segítségével hozhatja létre a fájlt a munkakönyvtárában:

```console
code deploy-aci.yaml
```

Adja meg a base64 kódolású fájlok tartalmát `secret`a . Például `cat` minden egyes base64-kódolt fájlokat, hogy annak tartalmát. A telepítés során ezek a fájlok hozzáadódnak egy [titkos kötethez](container-instances-volume-secret.md) a tárolócsoportban. Ebben a példában a titkos kötet a Nginx-tárolóhoz van csatlakoztatva.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>A tárolócsoport telepítése

Erőforráscsoport létrehozása az [az csoport létrehozása](/cli/azure/group#az-group-create) paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Telepítse a tárolócsoportot az [az container create](/cli/azure/container#az-container-create) paranccsal, és adja át a YAML-fájlt argumentumként.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Telepítési állapot megtekintése

A központi telepítés állapotának megtekintéséhez használja a következő [az container show](/cli/azure/container#az-container-show) parancsot:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

A sikeres telepítés hez a kimenet hasonló a következőhöz:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>SSL-kapcsolat ellenőrzése

A böngészősegítségével a tárolócsoport nyilvános IP-címére navigálhat. A példában látható IP-cím `52.157.22.76`a **https://52.157.22.76**, így az URL- cím a. A nginx-kiszolgáló konfigurációja miatt https-t kell használnia a futó alkalmazás megtekintéséhez. A HTTP-n keresztüli csatlakozásra tett kísérletek sikertelenek.

![Képernyőkép a böngészőről, ahol egy Azure-tárolópéldányban futó alkalmazás látható](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Mivel ez a példa önaláírt tanúsítványt használ, és nem egy hitelesítésszolgáltatótól, a böngésző biztonsági figyelmeztetést jelenít meg, amikor HTTPS-kapcsolaton keresztül csatlakozik a helyhez. Előfordulhat, hogy el kell fogadnia a figyelmeztetést, vagy módosítania kell a böngésző vagy a tanúsítvány beállításait a lapra való továbblépéshez. Ez várt működés.

>

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogyan állíthat be egy Nginx-tárolót az SSL-kapcsolatok engedélyezéséhez a tárolócsoportban futó webalkalmazáshoz. Ezt a példát olyan alkalmazásokhoz igazíthatja, amelyek a 80-as porttól eltérő porton figyelnek. A Nginx konfigurációs fájl jattot is frissítheti úgy, hogy a 80-as (HTTP) porton lévő kiszolgálókapcsolatokat automatikusan átirányítsa a HTTPS használatára.

Bár ez a cikk nginx-et használ az oldalkocsiban, használhat egy másik SSL-szolgáltatót, például [a Caddyt.](https://caddyserver.com/)

Ha a tárolócsoportot egy [Azure virtuális hálózatban](container-instances-vnet.md)telepíti, más lehetőségeket is megfontolhat egy SSL-végpont engedélyezéséhez egy háttértároló-példányhoz, beleértve a következőket:

* [Azure Functions proxyk](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) – tekintse meg a mintatelepítési [sablont.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)
