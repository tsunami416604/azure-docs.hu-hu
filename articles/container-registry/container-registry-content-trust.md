---
title: A tartalmak megbízhatósága az Azure Container Registryben
description: Ismerje meg, hogyan engedélyezheti a tartalommegbízhatóságot az Azure tárolóregisztrációs adatbázisokban, illetve hogyan küldhet és hívhat le aláírt rendszerképeket.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 08/20/2018
ms.author: danlep
ms.openlocfilehash: 6db5bb4ee1995e08bd00588203db1fdba87a3db5
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727334"
---
# <a name="content-trust-in-azure-container-registry"></a>A tartalmak megbízhatósága az Azure Container Registryben

A biztonsági szempontok mentén kialakított elosztott rendszerek esetében rendkívül fontos a rendszerbe belépő adatok *forrásának* és *integritásának* ellenőrzése. Elengedhetetlen, hogy az adatok felhasználói képesek legyenek ellenőrizni az adatok közzétevőjét (forrás), valamint azt is, hogy az adatok a közzétételük után nem lettek-e módosítva (integritás). Az Azure Container Registry mindkét célt támogatja a Docker [tartalommegbízhatósági][docker-content-trust] modelljének megvalósításával. Ez a cikk ennek bevezetőjéül szolgál.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="how-content-trust-works"></a>A tartalommegbízhatóság működése

A rendszerképek közzétevőjeként a tartalommegbízhatóság alkalmazásával **aláírhatja** a regisztrációs adatbázisba leküldött rendszerképeket. A rendszerképek felhasználói (az azokat a regisztrációs adatbázisból lehívó személyek vagy rendszerek) konfigurálhatják az ügyfeleket, hogy azok *csak* aláírt rendszerképeket kérjenek le. Amikor valamely rendszerkép-felhasználó lekér egy aláírt rendszerképet, a Docker-ügyfél ellenőrzi a rendszerkép integritását. Ebben a modellben a felhasználók biztosak lehetnek benne, hogy az adatbázisban található aláírt rendszerképeket valóban Ön tette közzé, és azok a közzétételüket követően nem lettek módosítva.

### <a name="trusted-images"></a>Megbízható rendszerképek

A tartalommegbízhatóság az adattárak **címkéivel** is használható. A rendszerkép-adattárak aláírt és nem aláírt címkékkel rendelkező rendszerképeket egyaránt tartalmazhatnak. Tegyük fel például, hogy csak a `myimage:stable` és a `myimage:latest` rendszerképet szeretné aláírni, a `myimage:dev` rendszerképet viszont nem.

### <a name="signing-keys"></a>Aláírókulcsok

A tartalommegbízhatóság titkosítási aláírókulcsok használatával valósul meg. A kulcsok egy adott adattárral vannak társítva az adatbázisban. A Docker-ügyfelek és az adatbázis által az adattárban lévő címkék megbízhatóságának kezeléséhez használt aláírókulcsoknak több típusa létezik. A tartalommegbízhatóság engedélyezése és a közzétételi és fogyasztási folyamatokba való integrálása esetén ezeket a kulcsokat gondosan kell felügyelnie. További információt a jelen cikk későbbi, [Kulcskezelés](#key-management) című szakaszában, valamint a Docker dokumentációjának [a tartalommegbízhatóság kulcsainak kezelését leíró részében][docker-manage-keys] talál.

> [!TIP]
> Ez a Docker tartalommegbízhatósági modelljének összefoglaló jellegű áttekintése volt. A tartalommegbízhatóság részletes leírásáért tekintse meg a [Docker tartalommegbízhatóságát ismertető cikket][docker-content-trust].

## <a name="enable-registry-content-trust"></a>Tárolóregisztrációs adatbázis tartalommegbízhatóságának engedélyezése

Első lépésként az adatbázis szintjén kell engedélyezni a tartalommegbízhatóságot. Miután engedélyezte a tartalommegbízhatóságot, az ügyfelek (felhasználók és szolgáltatások) aláírt rendszerképeket a küldhetnek az adatbázisba. Ha a tartalommegbízhatóság engedélyezve van az adatbázisban, az nem korlátozza az adatbázis használatát azokra a felhasználókra, akiknél az szintén engedélyezve van. Azok a felhasználók, akiknél nincs engedélyezve, továbbra is a szokott módon használhatják az adatbázist. Azok a felhasználók azonban, akik engedélyezték a tartalommegbízhatóságot az ügyfeleiken, *kizárólag* az aláírt rendszerképeket látják majd az adatbázisban.

A tartalommegbízhatóság az adatbázisban való engedélyezéséhez először lépjen az adatbázishoz az Azure Portalon. A **SZABÁLYZATOK** területen válassza a **Tartalommegbízhatóság (előzetes verzió)** > **Engedélyezve** > **Mentés** lehetőséget.

![Tartalommegbízhatóság engedélyezése egy adatbázishoz az Azure Portalon][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>A tartalommegbízhatóság engedélyezése az ügyfeleken

A megbízható rendszerképek használatához a rendszerképek közzétevőinek és felhasználóinak is engedélyezniük kell a tartalommegbízhatóságot a saját Docker-ügyfeleiken. A közzétevők aláírhatják az olyan adatbázisokba leküldött rendszerképeket, amelyeken a tartalommegbízhatóság engedélyezve van. A felhasználók a tartalommegbízhatóság engedélyezésével kizárólag az aláírt rendszerképeket láthatják az adatbázisban. A tartalommegbízhatóság alapértelmezés szerint le van tiltva a Docker-ügyfeleken, azonban felületi munkamenetenként vagy parancsonként engedélyezhető.

A tartalommegbízhatóság egy adott felületi munkamenethez való engedélyezéséhez állítsa a `DOCKER_CONTENT_TRUST` környezeti változót **1** értékűre. Például a Bash-felületen:

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

Ha inkább csak egyetlen parancshoz szeretné engedélyezni vagy letiltani a tartalommegbízhatóságot, több Docker-parancs is támogatja a `--disable-content-trust` argumentumot. A tartalommegbízhatóság engedélyezése egyetlen parancsra:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

Ha engedélyezte a tartalommegbízhatóságot a felületi munkamenethez, és le szeretné tiltani egyetlen parancs esetében:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>Rendszerkép-aláírási engedélyek kiosztása

Csak azok a felhasználók és rendszerek küldhetnek le megbízható rendszerképeket az adatbázisba, amelyeknek engedélyezte ezt. Ha egy felhasználónak (vagy egy szolgáltatásnevet használó rendszernek) engedélyezni szeretné a megbízható rendszerképek leküldését, ossza ki a felhasználó Azure Active Directory-identitásának az `AcrImageSigner` szerepkört. Ez a rendszerképek az adatbázisba való leküldését engedélyező `Contributor` (vagy `Owner`) szerepkörön felül szükséges.

Az alábbiakban ismertetjük az `AcrImageSigner` szerepkör az Azure Portalon és az Azure CLI felületen való kiosztásának részleteit.

### <a name="azure-portal"></a>Azure Portal

Navigáljon a beállításjegyzékhez, az Azure Portalon, majd válassza ki **hozzáférés-vezérlés (IAM)** > **szerepkör-hozzárendelés hozzáadása**. Alatt **szerepkör-hozzárendelés hozzáadása**válassza `AcrImageSigner` alatt **szerepkör**, majd **válassza** több felhasználóval vagy szolgáltatásnevek, majd **mentése**.

Ebben a példában két entitásnak osztottuk ki az `AcrImageSigner` szerepkört: egy „service-principal” nevű szolgáltatásnévnek és egy „Azure User” nevű felhasználónak.

![Tartalommegbízhatóság engedélyezése egy adatbázishoz az Azure Portalon][content-trust-02-portal]

### <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI használatával szeretne aláírási engedélyeket kiosztani egy felhasználónak, rendelje hozzá az `AcrImageSigner` szerepkört az adatbázisra vonatkozó hatókörrel. A parancs formátuma:

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

Ha például magának szeretné kiosztani a szerepkört, futtathatja az alábbi parancsokat egy hitelesített Azure CLI-munkamenetben. Módosítsa a `REGISTRY` értékét Azure tárolóregisztrációs adatbázisának nevére.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
USER=$(az account show --query user.name --output tsv)
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)

az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee $USER
```

Egy [szolgáltatásnévnek](container-registry-auth-service-principal.md) is kioszthat engedélyeket megbízható rendszerképek az adatbázisba való leküldésére. A szolgáltatásnevek használata az olyan összeállítási és egyéb felügyelet nélküli rendszerek esetén bizonyul hasznosnak, amelyeknek megbízható rendszerképeket kell leküldeniük az adatbázisba. A formátum hasonlít a felhasználói engedély kiosztása esetén használthoz, azonban az `--assignee` értékeként egy szolgáltatásnév-azonosítót kell megadni.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

A `<service principal ID>` lehet a szolgáltatásnév **appId** vagy **objectId** azonosítója, illetve valamely hozzá tartozó **servicePrincipalName**. A szolgáltatásnevek és az Azure Container Registry használatával kapcsolatos további információért tekintse meg [a szolgáltatásnevek az Azure Container Registryben való hitelesítését ismertető cikket](container-registry-auth-service-principal.md).

## <a name="push-a-trusted-image"></a>Megbízható rendszerképek leküldése

A megbízható rendszerkép címkéinek a tárolóregisztrációs adatbázisba való leküldéséhez engedélyezze a tartalommegbízhatóságot, és küldje le a rendszerképet a `docker push` paranccsal. Amikor első alkalommal küld le egy aláírt címkét, a rendszer arra kéri, hogy hozzon létre egy jelszót a legfelső szintű aláírókulcshoz és az adattár aláírókulcsához egyaránt. A legfelső szintű és az adattárkulcs létrehozása és tárolása egyaránt a helyi gépen történik.

```console
$ docker push myregistry.azurecr.io/myimage:v1
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

Miután létrejött az első olyan `docker push`, amelyhez engedélyezve van a tartalommegbízhatóság, a Docker-ügyfél ugyanazt a legfelső szintű kulcsot használja a következő leküldésekhez is. Az ugyanabban az adattárba irányuló következő leküldések esetében a rendszer már csak az adattár kulcsát kéri majd. Valahányszor új adattárba küld le megbízható rendszerképet, a rendszer arra kéri, hogy adjon meg egy jelszót egy új adattárkulcshoz.

## <a name="pull-a-trusted-image"></a>Megbízható rendszerképek lekérése

Megbízható rendszerkép lekéréséhez engedélyezze a tartalommegbízhatóságot, és futtassa a `docker pull` parancsot a szokásos módon. A tartalommegbízhatóságot engedélyezett felhasználók csak az aláírt címkékkel rendelkező rendszerképet kérhetik le. Íme egy példa egy aláírt címke lekérésére:

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

Ha egy tartalommegbízhatóságot engedélyezett ügyfél egy alá nem írt címkét próbál lekérni, a művelet meghiúsul:

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
No valid trust data for unsigned
```

### <a name="behind-the-scenes"></a>A színfalak mögött

A `docker pull` futtatásakor a Docker-ügyfél ugyanazt a kódtárat használja a lehívott címke címke–SHA-256 kivonatoló leképezésének igényléséhez, mint a [Notary CLI][docker-notary-cli] esetében. Miután ellenőrizte az aláírásokat a megbízhatósági adatokon, az ügyfél utasítja a Docker-motort, hogy végezzen el egy „kivonat szerinti lekérést”. A lekérés során a motor az SHA-256 ellenőrzőösszeget használja a tartalom címeként a rendszerkép jegyzékének az Azure tárolóregisztrációs adatbázisból való lekéréséhez és ellenőrzéséhez.

## <a name="key-management"></a>Kulcskezelés

Az első megbízható rendszerkép leküldésekor a `docker push` kimenetében található legfelső szintű kulcs a leginkább bizalmas. Mindenképp készítsen biztonsági másolatot a legfelső szintű kulcsról, és tárolja biztonságos helyen. Alapértelmezés szerint a Docker-ügyfél az aláírókulcsokat a következő könyvtárban tárolja:

```sh
~/.docker/trust/private
```

A legfelső szintű és az adattárkulcsokat tömörítse archívumba, és tárolja biztonságosan offline (például egy USB-tárolóeszközön). Például a Bashben:

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

A helyileg létrehozott legfelső szintű és adattárkulcsokkal együtt az Azure Container Registry sok más kulcsot is létrehoz és tárol a megbízható rendszerképek leküldésekor. A Docker tartalommegbízhatósági megoldásiban lévő különféle kulcsok részletes ismertetéséért, valamint további felügyeleti útmutatásért lásd a Docker dokumentációjának [a tartalommegbízhatóság kulcsainak kezelését ismertető szakaszát][docker-manage-keys].

### <a name="lost-root-key"></a>Elveszett legfelső szintű kulcs

Ha nem fér hozzá a legfelső szintű kulcshoz, az adott kulccsal aláírt címkékkel rendelkező adattárakban lévő aláírt címkékhez sem férhet hozzá. Az Azure Container Registry nem képes visszaállítani az elveszett legfelső szintű kulcsokkal aláírt rendszerképcímkék elérését. Az adatbázis összes megbízhatósági adatának (aláírásának) eltávolításához először tiltsa le, majd engedélyezze újra a tartalommegbízhatóságot az adatbázison.

> [!WARNING]
> Az adatbázis tartalommegbízhatóságának letiltása és ismételt engedélyezése **az adatbázis összes adattárában törli az összes aláírt címke megbízhatósági adatait**. Ez a művelet nem vonható vissza – az Azure Container Registry nem képes visszaállítani a törölt megbízhatósági adatokat. A tartalommegbízhatóság letiltásával maguk a rendszerképek nem lesznek törölve.

A tartalommegbízhatóság az adatbázisban való letiltásához lépjen az adatbázishoz az Azure Portalon. A **SZABÁLYZATOK** területen válassza a **Tartalommegbízhatóság (előzetes verzió)** > **Letiltva** > **Mentés** lehetőséget. A rendszer figyelmezteti, hogy az adatbázisban lévő összes aláírás elvész. Az adatbázis összes aláírásának végleges törléséhez kattintson az **OK** gombra.

![Tartalommegbízhatóság letiltása egy adatbázisban az Azure Portalon][content-trust-03-portal]

## <a name="next-steps"></a>További lépések

A tartalommegbízhatósággal kapcsolatos további információért tekintse meg a Docker dokumentációját. Bár több lényeges pontot is érintettünk ebben a cikkben, a tartalommegbízhatóság egy kiterjedtebb téma, amellyel részletesebben a Docker dokumentációja foglalkozik.

[Tartalommegbízhatóság a Dockerben][docker-content-trust]

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
