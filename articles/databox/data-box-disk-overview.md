---
title: A Microsoft Azure Data Box Disk áttekintése | Microsoft Docs
description: Az Azure Data Box Disk felhő alapú megoldást mutatja be, amellyel nagy mennyiségű adat helyezhető át az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 09/04/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: 322c398bd6384821697e2b672825cc0805eb43ad
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092898"
---
# <a name="what-is-azure-data-box-disk-preview"></a>Mi az az Azure Data Box Disk? (Előzetes verzió)

A Microsoft Azure Data Box Disk megoldással gyorsan, költséghatékonyan és megbízhatóan küldhet több terabájtnyi helyszíni adatot az Azure-ba. A biztonságos adatátvitel gyorsaságáról 1-5 SSD-lemez küldésével gondoskodunk. Ezeket a 8 TB-os titkosított lemezeket egy regionális szállító szolgáltatóval küldjük el az Ön adatközpontjába. 

Az Azure Portalon a Data Box szolgáltatás segítségével gyorsan konfigurálhatja, csatlakoztathatja és feloldhatja ezeket a lemezeket. Az adatokat a lemezekre másolhatja, és elküldheti őket az Azure-ba. Az Azure adatközpontjában adatait a lemezekről automatikusan feltöltjük a felhőbe egy gyors magánhálózati feltöltési hivatkozást használva.


> [!IMPORTANT]
> - A Data Box Disk szolgáltatás előzetes verzióban érhető el. A szolgáltatás telepítése előtt tekintse meg az [Azure előzetes verzióra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - A szolgáltatásra regisztrálnia kell. A feliratkozáshoz keresse fel az [Előzetes verzió portálját](http://aka.ms/azuredataboxfromdiskdocs).
> - Az előzetes verzió ideje alatt a Data Box Disk az USA és az Európai Unió területére szállítható ki. További információt a [Regionális elérhetőség](#region-availability) témakörben talál.

## <a name="use-cases"></a>Használati esetek

A Data Box Disk használatával több terabájtnyi adatot helyezhet át kapcsolat nélkül vagy korlátozott kapcsolattal is. Az adatáthelyezés lehet egyszeri, rendszeres, vagy egy kezdeti tömeges adatátvitelt követően rendszeres adatátvitel is. 

- **Egyszeri migrálás** – nagy mennyiségű helyszíni adat áthelyezése az Azure-ba. Ilyen lehet például, ha offline szalagokról archivált adatként helyezik át az adatokat az Azure ritka elérésű tárolójába.
- **Növekményes áthelyezés** – ha először tömeges adatáthelyezést végeznek a Data Box Disk használatával (kezdőérték), majd ezt követően a hálózaton keresztül növekményes adatáthelyezések történnek. Ha például a Commvault és a Data Box Disk használatával biztonsági másolatokat helyeznek át az Azure-ba. Az ilyen migrálást követően aztán növekményes adatokat helyeznek át az Azure tárhelyébe hálózaton keresztül. 
- **Rendszeres feltöltések** – ha rendszeresen keletkezik nagy mennyiségű adat, és azt az Azure-ba szükséges áthelyezni. Ez előfordulhat például az energiafeltárási munkálatoknál, ahol az olajfúrótornyok és a szélenergia-farmok kapcsán nagy mennyiségű videóanyag készül.

## <a name="the-workflow"></a>A munkafolyamat

A munkafolyamat általában az alábbi lépésekből áll:

1. **Megrendelés** – Hozzon létre egy megrendelést az Azure Portalon, adja meg a szállítási adatokat és az adatokhoz célként használandó Azure-tárfiókot. Ha a lemezek rendelkezésre állnak, az Azure titkosítja, előkészíti és kiszállítja a lemezeket egy szállítási nyomkövetési azonosítóval.

2. **Kézbesítés** – Ha a lemezek megérkeztek, csomagolja ki és csatlakoztassa őket ahhoz a számítógéphez, amelyen a másolandó adatok vannak. Oldja fel a lemezek zárolását.
    
3. **Adatok másolása** – A lemezre áthúzással másolhatja át az adatokat.

4. **Visszaküldés** – Előkészítés után küldje vissza a lemezeket az Azure-adatközpontba.

5. **Feltöltés** – A lemezekről az adatok automatikusan lesznek átmásolva az Azure-ba. A National Institute of Standards and Technology (NIST) útmutatóit követve a lemezeket biztonságosan töröljük.

A folyamat alatt minden lépéséről e-mailben kap értesítést. A folyamatot részletesebben is megismerheti az [A Data Box Disks üzembe helyezése az Azure Portalon](data-box-disk-quickstart-portal.md) című témakörben.


## <a name="benefits"></a>Előnyök

A Data Box Disk nagy mennyiségű adat Azure-ba való áthelyezésére szolgál a hálózat használata nélkül. A megoldás a következő előnyökkel jár:

- **Gyorsaság** – A Data Box Disk USB 3.0-ás kapcsolatot használ, amellyel akár 35 TB méretű adat is áthelyezhető az Azure-ba kevesebb mint egy hét alatt.   

- **Egyszerű használat** – A Data Box megoldás egyszerűen használható.

    - A lemezek USB-kapcsolatot használnak, amelyhez szinte egyáltalán nem szükséges beállítás.
    - A lemezek kis méretűek, így egyszerűen kezelhetők.
    - A lemezeknek nincs szüksége külső áramellátásra.
    - A lemezek használhatóak adatközpont-kiszolgálóval, asztali számítógéppel vagy laptoppal is.
    - A megoldás munkafolyamatát végig követheti az Azure Portalon.    

- **Biztonságos** – A Data Box Disk beépített biztonsági védelemmel rendelkezik a lemezekhez, az adatokhoz és a szolgáltatáshoz. 
    - A lemezek védve vannak a módosítások ellen, és biztonságos feltöltési funkcióval rendelkeznek. 
    - A lemezeken lévő adatokat a teljes folyamat alatt AES 128 bites titkosítás védi. 
    - A lemezeket csak az Azure Portalon elérhető kulcs használatával lehet feloldani. 
    - A szolgáltatást az Azure biztonsági funkciói védik. 
    - Miután az adatok fel lettek töltve az Azure-ba, a lemezeket az NIST 800-88r1 szabvány előírásainak megfelelően teljesen töröljük.  
    
További információt az [Azure Data Box biztonsága és adatvédelme](data-box-disk-security.md) című témakörben talál.


## <a name="features-and-specifications"></a>Funkciók és specifikációk


| Specifikációk                                          | Leírás              |
|---------------------------------------------------------|--------------------------|
| Tömeg                                                  | < 2 lb dobozonként. Legfeljebb 5 lemez egy dobozban                |
| Dimenziók                                              | Lemez – 2,5 inch SSD |            
| Kábelek                                                  | 1 USB 3.1 kábel lemezenként|
| Tárolási kapacitás megrendelésenként                              | 40 TB (használható ~ 35 TB)|
| Lemezek tárkapacitása                                   | 8 TB (használható ~ 7 TB)|
| Adatinterfész                                          | USB   |
| Biztonság                                                | Előre titkosítva BitLocker és biztonságos frissítés használatával <br> Hozzáférési kulcs által védett lemezek <br> Mindig titkosított adatok  |
| Adatátviteli sebesség                                      | maximum 430 MBps a fájlmérettől függően      |
|Kezelés                                               | Azure Portal |


## <a name="region-availability"></a>Régiónkénti elérhetőség

Az előzetes verzió ideje alatt a Data Box Disk használatával az alábbi Azure-régiókba lehet adatokat áthelyezni:


|Azure-régió  |Azure-régió  |
|---------|---------|
|USA nyugati középső régiója     |Közép-Kanada       |        
|USA 2. nyugati régiója     |Kelet-Kanada         |     
|USA nyugati régiója     | Nyugat-Európa        |      
|USA déli középső régiója   |Észak-Európa     |         
|USA középső régiója     |Kelet-Ausztrália|
|USA északi középső régiója  |Délkelet-Ausztrália   |
|USA keleti régiója      |Ausztrália középső régiója |
|USA 2. keleti régiója     |Ausztrália 2. középső régiója|


## <a name="pricing"></a>Díjszabás

Az előzetes verzió ideje alatt a Data Box Disk díjmentesen használható. Változás a Data Box Disk általános elérhetőségének idején várható.

## <a name="next-steps"></a>További lépések

- A [Data Box Disk követelményeinek](data-box-disk-system-requirements.md) áttekintése.
- A [Data Box Disk korlátainak](data-box-disk-limits.md) megismerése.
- Az [Azure Data Box Disk](data-box-disk-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.
