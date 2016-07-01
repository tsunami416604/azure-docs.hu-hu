<properties
    pageTitle="Azure Cool Storage Blob objektumokhoz | Microsoft Azure"
    description="A Blob Storage tárolási rétegei objektumadatok költséghatékony tárolását teszik lehetővé hozzáférési minták alapján. Az Azure Cool Storage a ritkábban használt adatok tárolására van optimalizálva."
    services="storage"
    documentationCenter=""
    authors="sribhat-msft"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sribhat"/>


# Azure Blob Storage: gyakran és ritkán használt adatok tárolási rétege

## Áttekintés

Az Azure Storage most két tárolási réteget kínál a blobtároláshoz (objektumtároláshoz), hogy adatait a legköltséghatékonyabb módon tárolhassa a használat függvényében. Az Azure **tárolási rétege gyakran használt adatokhoz** a gyakran használt adatok tárolására van optimalizálva. Az Azure **tárolási rétege ritkán használt adatokhoz** a ritkábban használt, hosszú élettartamú adatok tárolására van optimalizálva. A ritkán használt adatok tárolási rétegében lévő adatok esetében ugyan alacsonyabb rendelkezésre állás is elegendő lehet, azonban továbbra is magas tartósság és a gyakran használt adatokkal egyező hozzáférési idő és teljesítmény szükséges. A ritkán használt adatok esetében a valamelyest alacsonyabb rendelkezésre állási szolgáltatási szintek és magasabb hozzáférési költségek elfogadható kompromisszumot jelentenek az alacsonyabb tárolási költségek ellenében.

Napjainkban a felhőben tárolt adatok mennyisége exponenciálisan nő. A növekvő tárolási szükségletek költségeinek kezelése érdekében hasznos lehet az adatokat olyan attribútumok alapján szervezni, mint a hozzáférés gyakorisága vagy a tervezett megőrzési időtartam. A felhőben tárolt adatok az előállítás, a feldolgozás és a hozzáférés tekintetében igen különbözőek lehetnek az élettartamuk során. Egyes adatokat aktívan használnak és módosítanak teljes élettartamuk során. Egyes adatokat élettartamuk korai szakaszában sokat használnak, az adatok életkorának növekedésével azonban a hozzáférések mennyisége drasztikusan csökken. Egyes adatok pedig inaktívan a felhőben maradnak, és a tárolást követően csak nagyon ritkán használják őket, ha használják őket egyáltalán.

A fent vázolt adathozzáférési forgatókönyvek esetében számos előnyt biztosít az olyan rétegelt tárolási megoldás, amely egy adott hozzáférési mintára van optimalizálva. A gyakran és ritkán használt adatok tároláselérési rétegének bevezetésével az Azure Blob Storage most ezt a rétegelt tárolási igényt célozza meg különböző árképzési modellekkel.

## Blob Storage-fiókok

A **Blob Storage-fiókok** speciális tárfiókok a strukturálatlan adatok blobként (objektumokként) való tárolására az Azure Storage-ban. A Blob Storage-fiókokkal választhat a gyakran és ritkán használt adatok tároláselérési rétege közt, így a ritkábban használt adatait alacsonyabb tárolási költségek mellett, a gyakrabban használt adatait pedig alacsonyabb hozzáférési költségek mellett tárolhatja. A Blob Storage-fiókok hasonlóak a meglévő általános célú tárfiókjaihoz, és a jelenlegi rendszereivel megegyező szintű tartósságot, rendelkezésre állást, méretezhetőséget és teljesítményt nyújtanak, beleértve a 100%-os API-konzisztenciát a blokkblobokhoz és a hozzáfűző blobokhoz.

> [AZURE.NOTE] A Blob Storage-fiókok csak a blokkblobokat és a hozzáfűző blobokat támogatják, a lapblobokat nem.

A Blob Storage-fiókokban elérhető a **Hozzáférési réteg** attribútum, amely segítségével megadhatja, hogy a tárolási réteg **gyakran használt adatok** vagy **ritkán használt adatok** tárolására szolgáljon a fiókban tárolt adatoktól függően. Ha változik az adatok használati módja, bármikor válthat a hozzáférési rétegek közt.

> [AZURE.NOTE] A hozzáférési rétegek módosítása további díjakat vonhat maga után. További részleteket a következő szakaszban talál: [Árak és számlázás](storage-blob-storage-tiers.md#pricing-and-billing).

Példa használati forgatókönyvek a gyakran használt adatok tároláselérési rétegéhez:

- Adatok, amelyeket aktívan használnak, vagy amelyekhez várhatóan gyakran szeretnének hozzáférni (olvasás vagy írás formájában).
- A feldolgozásra és a későbbiekben a ritkán használt adatok tárolási rétegébe való áttelepítésre előkészített adatok.

Példa használati forgatókönyvek a ritkán használt adatok tároláselérési rétegéhez:

- Biztonsági mentési, archiválási és vészhelyreállítási adatkészletek.
- Régebbi, már csak ritkán megtekintett médiatartalmak, amelyek elérésére igény esetén azonban azonnal szükség van.
- Nagyobb adatkészletek, amelyeket költséghatékonyan kell tárolni, amíg a későbbi feldolgozáshoz szükséges többi adat gyűjtése még folyamatban van. (*Például* tudományos adatok vagy gyártási létesítményből származó nyers telemetriaadatok hosszú távú tárolása)
- Eredeti (nyers) adatok, amelyeket a végső használható formába való feldolgozásukat követően is meg kell őrizni. (*Például* nyers médiafájlok a más formátumba való átkódolásukat követően)
- Megfelelőségi és archiválási adatok, amelyeket hosszú ideig kell tárolni, azonban nagyon ritkán kell hozzáférni. (*Például* biztonsági kamerák felvételei, egészségügyi intézmények régi röntgen-/MRI-felvételei, pénzügyi szolgáltatók ügyfélszolgálati hívásainak hangfelvételei és átiratai)

A tárfiókokkal kapcsolatos további információk: [Tudnivalók az Azure Storage-fiókokról](storage-create-storage-account.md).

A csak blokk- és hozzáfűző blobok tárolását igénylő alkalmazásokhoz javasoljuk a Blob Storage-fiókok használatát, így kiaknázhatóak a rétegelt tárolás differenciált árképzési modelljének előnyei. Tisztában vagyunk azonban azzal, hogy ez bizonyos körülmények között esetleg nem lehetséges, ahol inkább az általános célú tárfiókok használata célszerű, például:

- Ha táblákat, üzenetsorokat vagy fájlokat kell használnia, és szeretné, ha a blobok ugyanabban a tárfiókban lennének tárolva. Vegye figyelembe, hogy ezek azonos tárfiókban való tárolásának egyetlen műszaki előnye, hogy ugyanazok a megosztott kulcsok szükségesek hozzájuk.
- Ugyanúgy a hagyományos telepítési modellt kell alkalmaznia. A Blob Storage-fiókok kizárólag az Azure Resource Manager-alapú üzemi modellben érhetőek el.
- Lapblobokat kell használnia. A Blob Storage-fiókok nem támogatják a lapblobokat. Általában a blokkblobok használatát javasoljuk, kivéve, ha valamiért kifejezetten lapblobokra van szüksége.
- A [Storage szolgáltatások REST API felülete](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014. 02. 14-nél korábbi verzióját vagy egy 4.x-nél korábbi verziójú ügyfélkódtárat használ, és nem tudja frissíteni az alkalmazást.

> [AZURE.NOTE] A Blob Storage-fiókokat jelenleg az Azure-régiók nagy része támogatja, és hamarosan újabb régiókban is támogatottak lesznek. Az elérhető régiók frissített listáját [Az Azure régiói](https://azure.microsoft.com/regions/#services) lapon találja.

## A hozzáférési rétegek összehasonlítása

A következő táblázat összehasonlítja a két hozzáférési réteget:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Gyakran használt adatok hozzáférési rétege</center></strong></td>
    <td><strong><center>Ritkán használt adatok hozzáférési rétege</center></strong></td
</tr>
<tr>
    <td><strong><center>Rendelkezésre állás</center></strong></td>
    <td><center>99.9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Rendelkezésre állás<br>(RA-GRS olvasások)</center></strong></td>
    <td><center>99.99%</center></td>
    <td><center>99.9%</center></td>
</tr>
<tr>
    <td><strong><center>Használati díjak</center></strong></td>
    <td><center>Magasabb tárolási költségek<br>Alacsonyabb hozzáférési és tranzakciós költségek</center></td>
    <td><center>Alacsonyabb tárolási költségek<br>Magasabb hozzáférési és tranzakciós költségek</center></td>
</tr>
<tr>
    <td><strong><center>Minimális objektumméret<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>Minimális tárolási időtartam<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>Késés<br>(az első bájtig eltelt idő)<center></strong></td>
    <td colspan="2"><center>ezredmásodperc</center></td>
</tr>
<tr>
    <td><strong><center>Méretezhetőségi és teljesítménycélok<center></strong></td>
    <td colspan="2"><center>Ugyanazok, mint az általános célú tárfiókok esetében</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] A Blob Storage-fiókok ugyanazokat a méretezhetőségi és teljesítménycélokat támogatják, mint az általános célú tárfiókok. További információkért lásd: [Azure Storage Scalability and Performance Targets](storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

## Árak és számlázás

A Blob Storage-fiókok új árképzési modellt alkalmaznak a blobtároláshoz a hozzáférési réteg alapján. A Blob Storage-fiókok használatakor az alábbi számlázási szempontok érvényesek:

- **Tárolási költségek**: Az adattárolás díja a tárolt adatok mennyisége mellett a hozzáférési rétegtől függ. A ritkán használt adatok tároláselérési rétegének gigabájtonkénti költsége alacsonyabb, mint a gyakran használt adatok tároláselérési rétegéé.
- **Adathozzáférési költségek**: A ritkán használt adatok tároláselérési rétege esetében gigabájtonkénti adathozzáférési díjat kell fizetni az adatolvasásokért és -írásokért.
- **Tranzakciós költségek**: Mindkét réteg esetében tranzakciónkénti díjat kell fizetni. Azonban a tranzakciónkénti költség magasabb a ritkán használt adatok, mint a gyakran használt adatok tároláselérési rétege esetében.
- **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.
- **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.
- **A hozzáférési réteg módosítása**: A hozzáférési réteg a ritkán használt adatok rétegéről a gyakran használt adatok rétegére való váltása esetében felmerülő díj minden váltás esetében megegyezik a tárfiókban lévő összes adat beolvasásának költségével. A gyakran használt adatok hozzáférési rétegéről a ritkán használt adatok hozzáférési rétegére való váltás azonban díjmentes.

> [AZURE.NOTE] Annak érdekében, hogy a felhasználók az elérhetővé tételt követően kipróbálhassák az új tárolási rétegeket és megismerhessék a funkciókat, a ritkán használt adatok hozzáférési rétegéről a gyakran használt adatok hozzáférési rétegére való váltás díját 2016. június 30-ig elengedjük. 2016. július 1-től a díj a gyakran használt adatok hozzáférési rétegéről a ritkán használt adatok hozzáférési rétegére való összes váltásra érvényes. A Blob Storage-fiókok árképzési modelljével kapcsolatos további részletek: [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/). A kimenő adatátviteli díjakkal kapcsolatos további részletekért lásd az [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) lapot.

## Első lépések

Ebben a szakaszban a bemutatjuk a következő forgatókönyveket az Azure portál használatával kapcsolatban:

- Blob Storage-fiók létrehozása.
- Blob Storage-fiók kezelése.

### Az Azure portál használata

#### Blob Storage-fiók létrehozása az Azure portál használatával

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. A Központ menüben válassza az **Új** -> **Adatok + tárolás** -> **Tárfiók** elemet.

3. Adja meg a tárfiók nevét.

4. Válassza a **Resource Manager** lehetőséget üzemi modellként.

5. Válassza a **Blob Storage** lehetőséget a tárfiók típusaként.

6. Válassza ki a hozzáférési réteget: **gyakran használt adatok** vagy **ritkán használt adatok**. Az alapértelmezett réteg a **gyakran használt adatok**.

7. Válassza ki a tárfiók replikálási beállítását: **LRS**, **GRS** vagy **RA-GRS**. Az alapértelmezett beállítás az **RA-GRS**. Az Azure Storage replikálási beállításaival kapcsolatos további részletek: [Azure Storage replication](storage-redundancy.md) (Az Azure Storage replikációja).

8. Válassza ki az előfizetést, amelyikben az új tárfiókot létre szeretné hozni.

9. Adjon meg egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. További információk az erőforráscsoportokkal kapcsolatban: [Az Azure portál használata az Azure-erőforrások kezeléséhez](../azure-portal/resource-group-portal.md).

10. Válassza ki a tárfiók földrajzi helyét.

11. Kattintson a **Létrehozás** parancsra a tárfiók létrehozásához.

#### Blob Storage-fiók hozzáférési rétegének módosítása az Azure portál használatával

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com), és lépjen a tárfiókhoz.

2. Kattintson **Az összes beállítás**, majd a **Konfiguráció** elemre a fiók konfigurációjának megtekintéséhez és/vagy módosításához.

3. Adja meg a kívánt hozzáférési réteget: **gyakran használt adatok** vagy **ritkán használt adatok**.

    > [AZURE.NOTE] A hozzáférési rétegek módosítása további díjakat vonhat maga után. További részleteket a következő szakaszban talál: [Árak és számlázás](storage-blob-storage-tiers.md#pricing-and-billing).

## Áttelepítés Blob Storage-fiókokra

Ennek a résznek a célja, hogy segítséget nyújtson a felhasználóknak a Blob Storage-fiókokra való zökkenőmentes váltásban. A Blob Storage-fiókok kifejezetten blokkblobok és hozzáfűző blobok tárolására készültek. A meglévő általános célú tárfiókok, amelyek a blobok mellett táblák, üzenetsorok, fájlok és lemezek tárolását is lehetővé teszik, nem konvertálhatóak Blob Storage-fiókká. A tárolási rétegek használatához létre kell hoznia egy új Blob Storage-fiókot, és áttelepíteni meglévő adatait az újonnan létrehozott fiókra.

### A meglévő adatok áttelepítésének megtervezése

Ha áthelyezi adatait egy Blob Storage-fiókba, valószínűleg érdemes kihasználnia a ritkán használt adatok tároláselérési rétege nyújtotta előnyt, hogy pénzt takaríthasson meg a ritkábban használt adatok tárolási költségeiből. Az első lépés az adatok egy Blob Storage-fiókba, a ritkán használt adatok tároláselérési rétegébe való áttelepítésekor, hogy feltérképezze az aktuális használati mintáját, és megállapítsa, hogy előnnyel jár-e az átállás a Blob Storage-fiókra. Általában a következőket érdemes figyelembe venni:

- Tárhelyhasználati minta – Mennyi adatot tárol, és ez milyen mértékben változik havi szinten?
- Tároló-hozzáférési minták – Mennyi adatot olvas és ír a fiókba (beleértve az új adatokat is)? Hány tranzakciót használ az adatok elérésére, és melyek ezek?

Meglévő tárfiókjai megfigyelésével és a fenti adatok összegyűjtésével kapcsolatban lásd: [Enabling Azure Storage metrics and viewing metrics data](storage-enable-and-view-metrics.md) (Az Azure Storage mérőszámainak engedélyezése és a mérőszámadatok megtekintése). Ezután ezen adatok alapján az [Azure Storage díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) használatával megbecsülheti költségeit.

### Meglévő adatok áttelepítése

Meglévő adatai a helyi tárolóeszközökről, külső felhőtárolási szolgáltatóktól vagy meglévő általános célú Azure-tárfiókjairól a Blob Storage-fiókokba való áttelepítéséhez a következő módszereket használhatja:

#### AzCopy

Az AzCopy egy Windows parancssori segédprogram, amely az adatok az Azure Storage szolgáltatásba vagy onnan máshová való nagyteljesítményű másolására lett kifejlesztve. Az AzCopy használatával adatait átmásolhatja Blob Storage-fiókjába meglévő általános célú tárfiókjából, vagy feltölthet adatokat helyi tárolóeszközeiről a Blob Storage-fiókba.

További részletekért lásd: [Transfer data with the AzCopy Command-Line Utility](storage-use-azcopy.md) (Adatátvitel az AzCopy parancssori segédprogrammal).

#### Adatátviteli könyvtár

Az Azure Storage a .NET-keretrendszerhez készült adatátviteli könyvtára az AzCopyt működtető alapvető adatátviteli keretrendszeren alapul. A könyvtár az AzCopyhoz hasonló nagy teljesítményű, megbízható és könnyű adatátviteli műveletekhez készült. Így az AzCopy által kínált szolgáltatások előnyeit teljes mértékben kiaknázhatja alkalmazásában natív módon, anélkül, hogy ehhez az AzCopy külső példányait kellene futtatnia vagy felügyelnie.

További részletek: [Azure Storage adatátviteli könyvtár a .Net-keretrendszerhez](https://github.com/Azure/azure-storage-net-data-movement)

#### REST API vagy ügyfélkódtár

Az adatok a Blob Storage-fiókra való áttelepítéséhez létrehozhat egy egyéni alkalmazást az Azure ügyfélkódtárai vagy az Azure Storage szolgáltatások REST API felülete segítségével. Az Azure Storage gazdag ügyfélkódtárakat biztosít több nyelvhez és platformhoz is, beleértve a következőket: .NET, Java, C++, Node.JS, PHP, Ruby és Python. Az ügyfélkódtárak olyan fejlett képességeket biztosítanak, mint az újrapróbálkozási logika, a naplózás vagy a párhuzamos feltöltések. Fejleszthet közvetlenül a REST API-n is, amely minden, HTTP-/HTTPS-kérelmek létrehozására alkalmas nyelven meghívható.

További részletekért lásd: [Ismerkedés az Azure Blob Storage szolgáltatással](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] Az ügyféloldali titkosítással titkosított blobok a titkosítással kapcsolatos metaadatokat a blobbal együtt tárolják. Ezért elengedhetetlen, hogy a másolási műveletek során a blob metaadatai, különösen a titkosítással kapcsolatos metaadatok megmaradjanak. Ha a blobokat a metaadatok nélkül másolja, a blob tartalma nem lesz újból lekérdezhető. A titkosítással kapcsolatos metaadatok további részletei: [Az Azure Storage ügyféloldali titkosítása](storage-client-side-encryption.md).

## Gyakori kérdések

1. **A meglévő tárfiókok továbbra is elérhetőek?**

    Igen, a meglévő tárfiókok továbbra is elérhetőek változatlan árképzéssel és funkcionalitással.  Nem érhető el hozzájuk a hozzáférési rétegek kiválasztásának lehetősége, és a jövőben sem rendelkeznek majd ezzel a képességgel.

2. **Miért és mikor érdemes elkezdenem Blob Storage-fiókot használni?**

    A Blob Storage-fiókok kifejezetten blobok tárolására készültek, így új blobközpontú szolgáltatások érhetők el hozzájuk. A továbbiakban a Blob Storage-fiók az ajánlott módszer a blobok tárolására, mivel a későbbi képességek, például a hierarchikus tárolás és a rétegválasztás ezen fióktípus alapján lesz bevezetve. Azonban Ön döntheti el üzleti igényei alapján, mikor kíván áttérni.

3. **Konvertálhatom meglévő tárfiókomat Blob Storage-fiókká?**

    Nem. A Blob Storage-fiók egy eltérő jellegű tárfiók, és ezért új fiókként kell létrehoznia, és az adatokat a fentiekben leírtak szerint áttelepítenie.

4. **Tárolhatok objektumokat mindkét hozzáférési rétegben egyazon fiókon?**

    A hozzáférési réteg attribútum a fiók szintjén van beállítva, és a fiókban lévő összes objektumra vonatkozik. A hozzáférési réteget nem lehet objektumszinten beállítani.

5. **Módosíthatom a Blob Storage-fiókom hozzáférési rétegét?**

    Igen. Lehetősége lesz módosítani a tárfiók hozzáférési rétegét. A hozzáférési réteg módosítása fiókszinten lehetséges, és az adott fiókban lévő összes objektumra vonatkozik majd. A hozzáférési réteg a gyakran használt adatok rétegéről a ritkán használt adatok rétegére való váltása nem jár többletköltséggel, a ritkán használt adatok rétegéről a gyakran használt adatok rétegére való váltás azonban gigabájtonkénti beolvasási költséggel jár a fiókban lévő összes adatra vonatkozóan.

6. **Milyen gyakran módosíthatom a Blob Storage-fiókom hozzáférési rétegét?**

    Bár nem határoztunk meg korlátozást a hozzáférési réteg módosításának gyakorisága tekintetében, vegye figyelembe, hogy a hozzáférési réteg a ritkán használt adatok rétegéről a gyakran használt adatok rétegére való váltása jelentős költségekkel jár. Nem javasoljuk, hogy gyakran váltson a hozzáférési rétegek között.

7. **A gyakran használt adatok tároláselérési rétegében és a ritkán használt adatok tároláselérési rétegében eltérően viselkednek a blobok?**

    A gyakran használt adatok tároláselérési rétegében a blobok ugyanolyan késéssel rendelkeznek, mint az általános célú tárfiókok esetében. A ritkán használt adatok tároláselérési rétegében a blobok hasonló késéssel rendelkeznek (ezredmásodpercben), mint az általános célú tárfiókok esetében.

    A ritkán használt adatok tároláselérési rétegében a blobok rendelkezésre állási szolgáltatási szintje (SLA-ja) kissé alacsonyabb, mint a gyakran használt adatok tároláselérési rétegében. További információkért lásd: [SLA a következőhöz: Storage](https://azure.microsoft.com/support/legal/sla/storage).

8. **Tárolhatok lapblobokat és virtuális gépek lemezeit a Blob Storage-fiókban?**

    A Blob Storage-fiókok csak a blokkblobokat és a hozzáfűző blobokat támogatják, a lapblobokat nem. Az Azure virtuális gépek lemezei lapblobokon alapulnak, ezért a Blob Storage-fiókok nem használhatóak virtuális gépek lemezeinek tárolására. Azonban lehetséges a virtuális gépek lemezeinek biztonsági másolatait blokkblobként tárolni a Blob Storage-fiókokban.

9. **Módosítanom kell a meglévő alkalmazásaimat a Blob Storage-fiókok használatához?**

    A Blob Storage-fiókok 100%-ban API-konzisztensek az általános célú tárfiókokkal a blokkblobok és a hozzáfűző blobok esetében. Ha az alkalmazás blokkblobokat vagy hozzáfűző blobokat használ, és a [Storage szolgáltatások REST API felülete](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014. 02. 14-i vagy újabb verzióját használja, az alkalmazásnak egyszerűen működnie kellene. Ha a protokoll egy régebbi verzióját használja, frissítenie kell az alkalmazást, hogy az az új verziót használja, és mindkét típusú tárfiókkal zökkenőmentesen működjön. Általánosságban véve mindig a legújabb verzió használatát javasoljuk, függetlenül a használt tárfiók típusától.

10. **Változik a felhasználói élmény?**

    A Blob Storage-fiókok nagyon hasonlóak az általános célú tárfiókokhoz a blokk- és hozzáfűző blobok tárolása tekintetében, és támogatják az Azure Storage minden lényeges szolgáltatását, beleértve a nagy tartósságot, valamint a magas szintű rendelkezésre állást, méretezhetőséget, teljesítményt és biztonságot. A Blob Storage-fiókokra jellemző konkrét szolgáltatásoktól és korlátozásoktól, valamint a fent kiemelt hozzáférési rétegektől eltekintve minden más változatlan marad.

## További lépések

### A Blob Storage-fiókok értékelése

[A Blob Storage-fiókok elérhetőségének ellenőrzése régió alapján](https://azure.microsoft.com/regions/#services)

[Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](storage-enable-and-view-metrics.md)

[A Blob Storage régió szerinti árképzésének megtekintése](https://azure.microsoft.com/pricing/details/storage/)

[Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)

### A Blob Storage-fiókok használatának megkezdése

[Ismerkedés az Azure Blob Storage szolgáltatással](storage-dotnet-how-to-use-blobs.md)

[Adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan máshová](storage-moving-data.md)

[Transfer data with the AzCopy Command-Line Utility (Adatátvitel az AzCopy parancssori segédprogrammal)](storage-use-azcopy.md)



<!--HONumber=Jun16_HO2-->


