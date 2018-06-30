---
title: Az Azure verem Ethereum megoldás sablon
description: Egyéni megoldás a sablonok segítségével telepítheti és konfigurálhatja a konzorcium Ethereum hálózati Azure veremben
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 6/28/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 4c2b0cda2d4144cde733f7f57ac6311e1a69f547
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114728"
---
# <a name="azure-stack-ethereum-solution-templates"></a>Az Azure verem Ethereum megoldás sablonok

A Ethereum megoldássablonban célja, hogy egyszerűbbé és gyorsabb telepítésére és konfigurálására egy többtagú konzorcium Ethereum hálózati minimális Azure és Ethereum ismeretekkel.

A felhasználói bevitel és egy kattintással indítható telepítési verem Azure felügyeleti portálján keresztül skálázhatja minden tag építhető ki a hálózat erőforrásigényét. A tranzakció elosztott terhelésű csomópontok áll minden tag hálózat erőforrásigényét, amely egy alkalmazás vagy felhasználó kommunikációját elküldeni a tranzakciók meg adatbányászati csomópontok műveletek és a hálózati virtuális készülék (NVA). A következő kapcsolódási lépést kapcsolatot hozhat létre teljesen konfigurált többtagú blockchain hálózatot NVAs.

## <a name="prerequisites"></a>Előfeltételek

Töltse le a következő [a piactérről](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS verzió 16.04.201802220
* Windows Server 2016 
* Egyéni parancsfájl Linux 2.0 
* Egyéni szkriptbővítmény 

Az Azure-on blockchain forgatókönyvekkel kapcsolatos további információkért lásd: [Ethereum igazolása a munkahelyi konzorcium megoldássablonban](../blockchain-workbench/ethereum-deployment-guide.md).

Több virtuális gépek telepítése által támogatott Azure-előfizetésre szükség. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

Ez a megoldás sablon telepíthet egy vagy több tag Ethereum konzorcium hálózati. A virtuális hálózat használatával a hálózati virtuális készüléket és a kapcsolat erőforrások lánc topológiában van csatlakoztatva. 

## <a name="deployment-use-cases"></a>Központi telepítés használati esetek

A sablon Ethereum konzorcium vezető és tag illesztés, többféle módon is telepíthet, itt azok általunk tesztelt:
- Egy több csomópontos Azure veremben, az Azure AD vagy AD FS-ben és központi telepítése az érdeklődési tag ugyanezzel az előfizetéssel vagy különböző előfizetésekhez.
- Egy egy csomópontos Azure veremben (az Azure AD) központi telepítése az érdeklődési és tag ugyanahhoz az előfizetéshez.

### <a name="standalone-and-consortium-leader-deployment"></a>Önálló és konzorcium vezető központi telepítés

A konzorcium vezető sablon az első tagtól erőforrásigényét konfigurálja a hálózaton. 

1. Töltse le a [vezető sablont a Githubból](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/ConsortiumLeader/mainTemplate.json)
2. Válassza ki a verem felügyeleti portálján, **új > sablon-üzembehelyezés** lehet üzembe helyezést végezni egy egyéni sablont.
3. Válassza ki **Szerkesztés sablon** szerkesztése az új egyéni sablont.
4. A szerkesztési ablaktáblán a jobb oldali másolja, és illessze be a vezető sablon korábban letöltött JSON.
    
    ![Vezető sablon szerkesztése](media/azure-stack-ethereum/edit-leader-template.png)

5. Kattintson a **Mentés** gombra.
6. Válassza ki **paraméterek szerkesztése** fejezze be a Sablonparaméterek az üzembe helyezéshez.
    
    ![Sablonparaméterek vezető szerkesztése](media/azure-stack-ethereum/edit-leader-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A telepített erőforrások elnevezési alapjaként használt karakterlánc. | Alfanumerikus karakterből állhat, és hossza 1 – 6 | ETH
    HITELESÍTÉS TÍPUSA | A módszer a virtuális gép felé történő hitelesítésre. | Jelszó vagy SSH nyilvános kulcs | Jelszó
    ADMINUSERNAME | Minden telepített virtuális gép rendszergazdai jogosultságú felhasználónevet | 1 – 64 karakter hosszúságú lehet | gethadmin
    ADMINPASSWORD (hitelesítési típus = jelszó)| A telepített virtuális gépek mindegyikének a rendszergazdai fiók jelszavát. A jelszónak tartalmaznia kell a 3. a következő követelményeknek: 1 nagybetűt, 1 kisbetűt, 1 számot vagy 1 különleges karaktert. <br />Bár minden virtuális gép kezdetben rendelkezik ugyanazzal a jelszóval, módosíthatja a jelszó kiépítése után.|12 - 72 karakter|
    ADMINSSHKEY (hitelesítési típus = sshPublicKey) | A biztonságos rendszerhéj kulcs távoli bejelentkezés. | |
    GENESISBLOCK | Egyéni képződés blokk megfelelő JSON-karakterláncot. | |
    ETHEREUMACCOUNTPSSWD | A rendszergazdai jelszó Ethereum fiókja védelmét. | |
    ETHEREUMACCOUNTPASSPHRASE | A Ethereum fiókhoz társított titkos kulcs létrehozásához használt jelszót. | |
    ETHEREUMNETWORKID | A konzorcium hálózati Azonosítóját. | Használja bármely értéket 5 és 999,999,999 között | 72
    CONSORTIUMMEMBERID | A társított minden egyes tagjára a konzorcium hálózati azonosító.   | Ezt az Azonosítót a hálózati egyedinek kell lenniük. | 0
    NUMMININGNODES | Adatbányászati csomópontok száma. | 2 és 15. | 2
    MNNODEVMSIZE | Az adatbányászati csomópontok Virtuálisgép-méretet. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Tárolási teljesítményének adatbányászati csomópontot. | | Standard_LRS
    NUMTXNODES | A tranzakció csomópontok számát. | 1 és 5. | 1
    TXNODEVMSIZE | A tranzakció csomópontok Virtuálisgép-méretet. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | A tranzakció csomópontok tárolási teljesítményt. | | Standard_LRS
    BASEURL | Alap URL-címet a függően sablonok beszerzését. | Az alapértelmezett értéket használja, kivéve, ha szeretné testre szabni a központi telepítési sablonok. | 

7. Kattintson az **OK** gombra.
8. A **egyéni telepítési**, adja meg **előfizetés**, **erőforráscsoport**, és **erőforráscsoport helye**.
    
    ![Vezető üzembe helyezéshez megadott paraméterek](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    Előfizetés | Az előfizetést, amelyhez a konzorcium hálózati telepítése | | Felhasználás előfizetés
    Erőforráscsoport | Az erőforráscsoport, amelyre a konzorcium hálózati telepíteni szeretné. | | EthereumResources
    Hely | Az Azure-régió az erőforráscsoport. | | helyi

8. Kattintson a **Létrehozás** gombra.

Központi telepítés 20 percig is eltarthat, vagy hosszabb ideig.

Központi telepítés befejezése után áttekintheti a telepítés összegzése **Microsoft. Sablon** az erőforráscsoport telepítési szakaszában. Az összegzés kimeneti értékeket tartalmaz, amelyek segítségével csatlakoztassa konzorcium tagok.

Vezetői telepítés ellenőrzéséhez keresse meg a vezetői felügyeleti hely. Felügyeleti webhely címe kimeneti szakaszában található **Microsoft.Template** központi telepítés.  

![Vezető telepítési összefoglaló](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Csatlakozás konzorcium tag központi telepítés

1. Töltse le a [konzorcium tag sablont a Githubból](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/JoiningMember/mainTemplate.json)
2. Válassza ki a verem felügyeleti portálján, **új > sablon-üzembehelyezés** lehet üzembe helyezést végezni egy egyéni sablont.
3. Válassza ki **Szerkesztés sablon** szerkesztése az új egyéni sablont.
4. A szerkesztési ablaktáblán a jobb oldali másolja, és illessze be a vezető sablon korábban letöltött JSON.
5. Kattintson a **Mentés** gombra.
6. Válassza ki **paraméterek szerkesztése** fejezze be a Sablonparaméterek az üzembe helyezéshez.

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A telepített erőforrások elnevezési alapjaként használt karakterlánc. | Alfanumerikus karakterből állhat, és hossza 1 – 6 | ETH
    HITELESÍTÉS TÍPUSA | A módszer a virtuális gép felé történő hitelesítésre. | Jelszó vagy SSH nyilvános kulcs | Jelszó
    ADMINUSERNAME | Minden telepített virtuális gép rendszergazdai jogosultságú felhasználónevet | 1 – 64 karakter hosszúságú lehet | gethadmin
    ADMINPASSWORD (hitelesítési típus = jelszó)| A telepített virtuális gépek mindegyikének a rendszergazdai fiók jelszavát. A jelszónak tartalmaznia kell a 3. a következő követelményeknek: 1 nagybetűt, 1 kisbetűt, 1 számot vagy 1 különleges karaktert. <br />Bár minden virtuális gép kezdetben rendelkezik ugyanazzal a jelszóval, módosíthatja a jelszó kiépítése után.|12 - 72 karakter|
    ADMINSSHKEY (hitelesítési típus = sshPublicKey) | A biztonságos rendszerhéj kulcs távoli bejelentkezés. | |
    CONSORTIUMMEMBERID | A társított minden egyes tagjára a konzorcium hálózati azonosító.   | Ezt az Azonosítót a hálózati egyedinek kell lenniük. | 0
    NUMMININGNODES | Adatbányászati csomópontok száma. | 2 és 15. | 2
    MNNODEVMSIZE | Az adatbányászati csomópontok Virtuálisgép-méretet. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Tárolási teljesítményének adatbányászati csomópontot. | | Standard_LRS
    NUMTXNODES | A tranzakció csomópontok számát. | 1 és 5. | 1
    TXNODEVMSIZE | A tranzakció csomópontok Virtuálisgép-méretet. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | A tranzakció csomópontok tárolási teljesítményt. | | Standard_LRS
    CONSORTIUMDATA | A megadott tag egy másik telepítés által érintett konzorcium adatok mutató URL-címet. Ez az érték vezetői telepítési kimeneti található. | |
    REMOTEMEMBERVNETADDRESSSPACE | A kitöltés NVA IP-címét. Ez az érték vezetői telepítési kimeneti található. | | 
    REMOTEMEMBERNVAPUBLICIP | A kitöltés NVA IP-címét. Ez az érték vezetői telepítési kimeneti található. | | 
    CONNECTIONSHAREDKEY | Egy előre meghatározott titkos kulcsot egy kapcsolatot létesít a konzorcium hálózati tagjai között. | |
    BASEURL | Alap URL-címet a sablonhoz. | Az alapértelmezett értéket használja, kivéve, ha szeretné testre szabni a központi telepítési sablonok. | 

7. Kattintson az **OK** gombra.
8. A **egyéni telepítési**, adja meg **előfizetés**, **erőforráscsoport**, és **erőforráscsoport helye**.

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    Előfizetés | Az előfizetést, amelyhez a konzorcium hálózati telepítése | | Felhasználás előfizetés
    Erőforráscsoport | Az erőforráscsoport, amelyre a konzorcium hálózati telepíteni szeretné. | | MemberResources
    Hely | Az Azure-régió az erőforráscsoport. | | helyi

8. Kattintson a **Létrehozás** gombra.

Központi telepítés 20 percig is eltarthat, vagy hosszabb ideig.

Központi telepítés befejezése után áttekintheti a telepítés összegzése **Microsoft.Template** az erőforráscsoport telepítési szakaszában. Az összegzés konzorciumi tagoknak csatlakozáshoz használható kimeneti értékeket tartalmaz.

Tag telepítés ellenőrzéséhez keresse meg a tag felügyeleti hely. Felügyeleti webhely címe Microsoft.Template telepítési kimeneti szakaszában találja.

![Tag központi telepítési összefoglaló](media/azure-stack-ethereum/ethereum-node-status-2.png)

A képen látható, tagok csomópontok állapota **nem fut**. Ennek oka az, tagok és a vezető közötti kapcsolat nem jön létre. Tagok és a vezető közötti kapcsolat egy kétirányú kapcsolat. Tag telepítésekor sablon automatikusan létrehozza a vezető tag a kapcsolatot. A kapcsolat vezető tagja és hozhatja létre a következő lépéssel.

### <a name="connect-member-and-leader"></a>Csatlakozás a tagok és a vezető

Ez a sablon kapcsolatot hoz létre a vezetője távoli taghoz. 

1. Töltse le a [csatlakozzon a tagok és a vezető sablont a Githubból](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/Connection/mainTemplate.json)
2. Válassza ki a verem felügyeleti portálján, **új > sablon-üzembehelyezés** lehet üzembe helyezést végezni egy egyéni sablont.
3. Válassza ki **Szerkesztés sablon** szerkesztése az új egyéni sablont.
4. A szerkesztési ablaktáblán a jobb oldali másolja, és illessze be a vezető sablon korábban letöltött JSON.
    
    ![Szerkesztés csatlakozás sablon](media/azure-stack-ethereum/edit-connect-template.png)

5. Kattintson a **Mentés** gombra.
6. Válassza ki **paraméterek szerkesztése** fejezze be a Sablonparaméterek az üzembe helyezéshez.
    
    ![Szerkesztés csatlakozás Sablonparaméterek](media/azure-stack-ethereum/edit-connect-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Vezetői előtagja. Ez az érték vezetői telepítési kimeneti található.  | Alfanumerikus karakterből állhat, és hossza 1 – 6 | |
    MEMBERROUTETABLENAME | A kitöltés az útvonaltábla neve. Ez az érték vezetői telepítési kimeneti található. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | A tag címterének. Ezt az értéket a tag telepítési kimeneti található. | |
    CONNECTIONSHAREDKEY | Egy előre meghatározott titkos kulcsot egy kapcsolatot létesít a konzorcium hálózati tagjai között.  | |
    REMOTEMEMBERNVAPUBLICIP | A tag NVA IP-címét. Ezt az értéket a tag telepítési kimeneti található. | |
    MEMBERNVAPRIVATEIP | Vezetői magánhálózati NVA IP-címet. Ez az érték vezetői telepítési kimeneti található. | |
    HELY | Az Azure-verem környezet helyre. | | helyi
    BASEURL | Alap URL-címet a sablonhoz. | Az alapértelmezett értéket használja, kivéve, ha szeretné testre szabni a központi telepítési sablonok. | 

7. Kattintson az **OK** gombra.
8. A **egyéni telepítési**, adja meg **előfizetés**, **erőforráscsoport**, és **erőforráscsoport helye**.
    
    ![Csatlakozás az üzembe helyezéshez megadott paraméterek](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    Előfizetés | A kitöltés előfizetés. | | Felhasználás előfizetés
    Erőforráscsoport | A kitöltés erőforráscsoportot. | | EthereumResources
    Hely | Az Azure-régió az erőforráscsoport. | | helyi

8. Kattintson a **Létrehozás** gombra.

Központi telepítés befejezése után vezető tag és elindítani a kommunikációt néhány percet vesz igénybe. A telepítés ellenőrzése, hogy a tag felügyeleti hely frissítése. A tag csomópontok állapotának rendszerűnek kell lennie. 

![A telepítés ellenőrzése](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>További lépések

- Ethereum és az Azure kapcsolatos további információkért lásd: [Blockchain technológia és az alkalmazások |} A Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Az Azure-on blockchain forgatókönyvekkel kapcsolatos további információkért lásd: [Ethereum igazolása a munkahelyi konzorcium megoldássablonban](../blockchain-workbench/ethereum-deployment-guide.md).