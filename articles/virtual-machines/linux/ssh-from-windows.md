---
title: SSH-kulcsok használata a Windows rendszerben Linux virtuális gépek |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és használhat SSH-kulcsok Windows rendszerű számítógépeken egy Linux virtuális gépet az Azure való kapcsolódáshoz.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: danlep
ms.openlocfilehash: fcc2365c3b41fb69492aa68bf7c48c2d3b8ee5f3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Az SSH kulcsok a Windows Azure-on
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

Linux virtuális gépek (VM) az Azure-ban való csatlakozáskor használandó [nyilvános kulcsú](https://wikipedia.org/wiki/Public-key_cryptography) jelentkezzen be a Linux virtuális Gépet egy biztonságosabb megoldást biztosítson. A folyamat során a nyilvános és titkos kulcsok cseréjét saját maga helyett egy felhasználónév és jelszó hitelesítéséhez a secure shell (SSH) parancs használatával. Ki téve találgatásos támadások, különösen az internetre irányuló virtuális gépeken futó, például jelszavakat. Ez a cikk áttekintést nyújt az SSH-kulcsok és a Windows rendszerű számítógépeken a megfelelő kulcsok létrehozása.

## <a name="overview-of-ssh-and-keys"></a>SSH és a kulcsok – áttekintés
Biztonságosan bejelentkezhet a Linux virtuális gép nyilvános és titkos kulcsok használatával:

* A **nyilvános kulcs** helyezkedik el a Linux virtuális Gépet, vagy bármely olyan szolgáltatás, amely a nyilvános kulcsú titkosítás használna.
* A **titkos kulcs** van Mi mutassa be a Linux virtuális Gépre történő bejelentkezéskor, a személyazonosságát. Védje a titkos kulcsot. Ne ossza meg senkivel.

A nyilvános és titkos kulcsokat több virtuális gépek és szolgáltatások is használható. Nem kell egy kulcspárra van szüksége az egyes virtuális gép vagy szolgáltatás el szeretné érni. Részletesebb áttekintéséért lásd: [nyilvános kulcsú](https://wikipedia.org/wiki/Public-key_cryptography).

Az SSH egy olyan titkosított kapcsolati protokoll, amely lehetővé teszi, hogy a biztonságos bejelentkezés titkosítatlan kapcsolaton keresztül. Az Azure szolgáltatásban üzemeltetett Linux virtuális gépek alapértelmezett csatlakozási protokoll. Habár maga SSH titkosított kapcsolatot biztosít, jelszavak használata SSH-kapcsolatok továbbra is védtelen a virtuális Gépet a találgatásos támadásokkal vagy a jelszavak találgatás. A biztonságosabb és előnyben részesített csatlakoztatása egy virtuális gép SSH módszer a nyilvános és titkos kulcsok, más néven az SSH-kulcsok használatával.

Ha nem szeretne SSH-kulcsok használata, továbbra is bejelentkezhet a Linux virtuális gépek jelszó használatával. Ha a virtuális gép nem kapcsolódik az internethez, elegendő jelszóval lehet. Azonban továbbra is szeretné az egyes Linux virtuális gépek a jelszavak kezelése és karbantartása a megfelelő jelszót házirendeket és gyakorlatokat, például a jelszó minimális hossza, és rendszeresen frissíteni azokat. SSH-kulcsok használata révén egyszerűbben egyéni hitelesítő adatok kezelése több virtuális gépek között.

## <a name="windows-packages-and-ssh-clients"></a>Windows-csomagok és az SSH-ügyfél
Csatlakozhat, és a Linux virtuális gépek kezelése az Azure használatával egy **SSH-ügyfél**. Windows rendszerű számítógépek általában nincs egy SSH-ügyfél telepítve. A Windows 10 évforduló frissítés hozzáadott Bash a Windows, és a Windows 10 Creators legfrissebb további frissítéseit tartalmazza. A Linux Windows alrendszere futtatására és segédprogramok például egy SSH-ügyfél alapértelmezés szerint a rendszerhéjakba belül elérésére teszi lehetővé. Kövesse a Linux docs bármelyikét például [SSH-kulcspár létrehozása Linux](mac-create-ssh-keys.md). Bash Windows még fejlesztés alatt áll, ezért a bétaverzió tekinthető. A Windows Bash kapcsolatos további információkért lásd: [Windows Ubuntu a Bash](https://msdn.microsoft.com/commandline/wsl/about).

Ha valami nem Bash Windows használni kívánt, közös Windows SSH ügyfelek telepítése tartalmazza a következő csomagokhoz:

* [A Windows Git](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>Mely kulcsfontosságú fájlokat szeretné létrehozni?
Az Azure-nak, legalább 2048 bites **ssh-rsa** formátumú nyilvános és titkos kulcsok. A klasszikus telepítési modell használata Azure-erőforrások kezelése, is kell létrehozni egy PEM (`.pem` fájl).

Az alábbiakban a központi telepítési forgatókönyvek és használ az egyes fájltípusok:

1. **ssh-rsa** bármely használó telepítés esetében szükség a [Azure-portálon](https://portal.azure.com), és a Resource Manager üzembe helyezése a [Azure CLI](../../cli-install-nodejs.md).
   * Ezek a kulcsok rendszerint minden legtöbbek számára szükséges.
2. A `.pem` fájl a klasszikus telepítési használó virtuális gépek létrehozásához szükséges. Ezek a kulcsok használata esetén támogatottak klasszikus telepítések esetén a [Azure-portálon](https://portal.azure.com) vagy [Azure CLI](../../cli-install-nodejs.md).
   * Csak a további kulcsok és a tanúsítványok létrehozásához klasszikus telepítési modellel létrehozott erőforrások kezelése esetén szükséges.

## <a name="install-git-for-windows"></a>Git for Windows telepítése
Az előző szakaszban felsorolt több csomagot, amely tartalmazza a `openssl` eszköz Windows rendszerhez. Ez az eszköz nyilvános és titkos kulcsok létrehozásához szükséges. A következő példák részletesen telepítése és használata **Git for Windows**, abban az esetben, ha bármelyik csomag inkább választhat. **Git for Windows** elérheti a szoftverek némelyike kiegészítő nyílt forráskódú ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) eszközök és segédeszközök Linux virtuális gépek használata hasznos lehet.

1. Töltse le és telepítse **Git for Windows** a következő helyről: [ https://git-for-windows.github.io/ ](https://git-for-windows.github.io/).
2. Fogadja el az alapértelmezett beállításokat a telepítés során, kivéve, ha kifejezetten módosítani szeretné őket.
3. Futtatás **Git bash eszközt** a a **Start menü** > **Git** > **Git bash eszközt**. A konzol az alábbihoz hasonlít:

    ![Git a Windows Bash rendszerhéjat](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>A titkos kulcs létrehozása
1. Az a **Git bash eszközt** ablakban használata `openssl.exe` titkos kulcs létrehozásához. Az alábbi példakód létrehozza nevű kulcs `myPrivateKey` és nevű tanúsítvány `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    A kimeneti az alábbihoz hasonlít:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Ha bash hiba azt jelenti, próbálkozzon egy új **Git bash eszközt** ablakot emelt szintű jogosultságokkal. Ezután futtassa újra a `openssl` parancsot.

2. Válaszolja meg a megjelenő utasításokat az ország-név, a helyet, a szervezet neve, a stb.
3. Az új titkos kulcsot és tanúsítványt az aktuális munkakönyvtárban jönnek létre. Biztonsági okból kell az engedélyek beállítása a titkos kulcs, hogy csak Ön férhessen hozzá:

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. A [következő szakasz](#create-a-private-key-for-putty) PuTTYgen használatával megtekintése és használata a nyilvános kulcsot, és hozzon létre egy titkos kulcsot adott PuTTY SSH a Linux virtuális gépekhez való használatára vonatkozó részletek. A következő parancs nevű nyilvános kulcsfájlt hoz létre `myPublicKey.key` azonnal használható:

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. Ha is szükség van, klasszikus-erőforrások kezeléséhez, alakítsa át a `myCert.pem` való `myCert.cer` (DER kódolású X509 tanúsítvány). Hajtsa végre ezt a lépést nem kötelező, csak akkor, ha kifejezetten a régebbi klasszikus-erőforrások kezeléséhez van szüksége.

    Alakítsa át a tanúsítványt a következő parancsot:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Hozzon létre egy titkos kulcsot a putty rendszerhez
A puTTY gyakori SSH-ügyfél Windows rendszerre. Szabadon bármely SSH-ügyfél, amely kívánja használni. A PuTTY használata szüksége egy további típusú kulcs - a PuTTY titkos kulcs (PPK) létrehozásához. Ha nem kíván a PuTTY használata, hagyja ki ezt a szakaszt.

Az alábbi példakód létrehozza a további titkos kulcs kifejezetten a PuTTY használata:

1. Használjon **Git bash eszközt** konvertálni a titkos kulcsot egy RSA-titkos kulcsot, amely a PuTTYgen tudja értelmezni. Az alábbi példakód létrehozza nevű kulcs `myPrivateKey_rsa` nevű meglévő kulcsból `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Biztonsági okból kell az engedélyek beállítása a titkos kulcs, hogy csak Ön férhessen hozzá:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. Töltse le és futtassa a PuTTYgen a következő helyről: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. A menü: **fájl** > **terhelés titkos kulcs**
4. Keresse meg a titkos kulcsot (`myPrivateKey_rsa` az előző példában). Az alapértelmezett címtár indításakor **Git bash eszközt** van `C:\Users\%username%`. Módosítsa a fájl szűrőt **minden fájl (\*.\*)** :

    ![A meglévő titkos kulcs betöltése a PuTTYgen](./media/ssh-from-windows/load-private-key.png)
5. Kattintson a **nyitott**. A kérdés azt jelzi, hogy a kulcs sikeresen importálva lett:

    ![Az importálás sikeres kulcs PuTTYgen](./media/ssh-from-windows/successfully-imported-key.png)
6. Kattintson a **OK** kattintva zárja be a parancssorba.
7. A nyilvános kulcs tetején jelenik meg a **PuTTYgen** ablak. Másolja és illessze be a nyilvános kulcsot az Azure-portálon vagy az Azure Resource Manager sablon Linux virtuális gép létrehozásakor. Is **mentés nyilvános kulcs** , mentse a számítógéphez:

    ![PuTTY nyilvánoskulcs-fájl mentése](./media/ssh-from-windows/save-public-key.png)

    A következő példa bemutatja, hogyan, akkor másolja és illessze be a nyilvános kulcs be az Azure portálon Linux virtuális gép létrehozásakor. A nyilvános kulcsot általában majd tárolja `~/.ssh/authorized_keys` a új virtuális gépén.

    ![Használja a nyilvános kulcsot, ha a virtuális gép létrehozása az Azure-portálon](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. Vissza a **PuTTYgen**, kattintson a **titkos kulcs mentése**:

    ![A PuTTY titkos kulcs fájl mentése](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > A kérdés kéri, ha továbbra is szeretné a kulcsot a jelszó megadása nélkül. Jelszó szükséges a jelszót a titkos kulcs csatolva hasonlít. Akkor is, ha valaki volt, a titkos kulcs beszerzése, azok továbbra is nem tudná hitelesítést csak a kulcsot. A jelszó is kell. Nélkül egy hozzáférési kódot Ha valaki beszerzi a titkos kulcsot, akkor is bejelentkezhetnek a valamennyi virtuális gép által használt kulcs. Azt javasoljuk, hogy hozzon létre egy hozzáférési kódot. Ha azonban elfelejti a hozzáférési kódját, nincs lehetőség a helyreállításra.
   >
   >

    Ha szeretne adni egy jelszót, kattintson a **nem**, adja meg a PuTTYgen főablakban, és kattintson a **mentés titkos kulcs** újra. Ellenkező esetben kattintson a **Igen** anélkül, hogy a jelszót a folytatáshoz.
9. Adjon nevet és a PPK fájl mentési helyét.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>A Putty használata az SSH a Linux-gépekhez
A PuTTY újra, a gyakori SSH-ügyfél Windows. Szabadon bármely SSH-ügyfél, amely kívánja használni. Az alábbi lépéseket a titkos kulcsa segítségével hitelesítik magukat az Azure virtuális gép SSH használatával módjáról részletesen. A lépések hasonlóak a más SSH kulcs ügyfelek tekintetében kell betölteni a titkos kulcs az SSH-kapcsolat hitelesítéséhez.

1. A letöltés és futtatási putty a következő helyről: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Adja meg az állomásnevet vagy IP-címet a virtuális gép az Azure-portálon:

    ![Új PuTTY kapcsolat megnyitása](./media/ssh-from-windows/putty-new-connection.png)
3. Mielőtt kiválasztja **nyissa meg**, kattintson a **kapcsolat** > **SSH** > **Auth** lapon. Keresse meg, és a titkos kulcs kiválasztásához:

    ![Válassza ki a PuTTY titkos kulcsot a hitelesítéshez](./media/ssh-from-windows/putty-auth-dialog.png)
4. Kattintson a **nyitott** a virtuális géphez való kapcsolódáshoz

## <a name="next-steps"></a>További lépések
A nyilvános és titkos kulcsokat is létrehozhat [OS X-és Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

További információ a Bash a Windows és a Windows-számítógépen OSS eszközöket járó előnyöket: [Windows Ubuntu a Bash](https://msdn.microsoft.com/commandline/wsl/about).

Ha gondja támad az SSH használata a Linux virtuális gépek csatlakozni, lásd: [hibaelhárítása SSH kapcsolódik az Azure Linux virtuális gép](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
