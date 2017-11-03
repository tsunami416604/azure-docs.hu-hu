# <a name="using-cdn-for-azure"></a>Az Azure CDN használatával
Az Azure tartalom Delivery Network (CDN) tartalmak nagy sávszélességű kézbesítéséhez a blobok és számítási példányokért fizikai csomópontokon az Egyesült Államok, Európa, Ázsia, Ausztrália és Dél-Amerika a statikus tartalom gyorsítótárazása révén globális megoldást kínál a fejlesztők. CDN-csomóponti helyek aktuális listájának megtekintéséhez lásd: [Azure CDN-csomóponti helyek].

Ez a feladat a következő lépésekből áll:

* [1. lépés: Tárfiók létrehozása](#Step1)
* [2. lépés: A tárfiók új CDN-végpont létrehozása](#Step2)
* [3. lépés: A CDN-tartalom elérése](#Step3)
* [4. lépés: A CDN-tartalom eltávolítása](#Step4)

A CDN használata az Azure data gyorsítótárazásához előnyei a következők:

* Jobb teljesítmény és a felhasználói élmény a végfelhasználók számára, akik a tartalomforrás távol, és olyan alkalmazásokat használ, ahol sok "internet utazgatással" szükséges tartalom betöltése
* Nagy méretű elosztott méretezési jobban azonnali magas terhelés kezelésére, tegyük fel például, az esemény például a termékbevezetés elején.

Meglévő CDN az ügyfelek most már használhatja az Azure CDN-t a [a klasszikus Azure portálon]. A CDN egy bővítmény szolgáltatás az előfizetéshez, és egy különálló [számlázási csomag].

<a id="Step1"> </a>

<h2>1. lépés: Tárfiók létrehozása</h2>

A következő eljárással hozzon létre egy új tárfiókot, Azure-előfizetéssel. A storage-fiók az Azure storage-szolgáltatásokhoz való hozzáférést. A tárfiók eléréséhez szükséges az Azure storage szolgáltatás összetevői a névtér a legmagasabb szintű jelöli: Blob-szolgáltatások, Queue szolgáltatások és Table szolgáltatások. Az Azure storage szolgáltatásainak kapcsolatos további információkért lásd: [az Azure Storage szolgáltatásainak használatával](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Hozzon létre egy tárfiókot, vagy a szolgáltatás rendszergazdájának vagy társadminisztrátorának a társított előfizetés kell lennie.

> [!NOTE]
> A művelet végrehajtása az Azure szolgáltatásfelügyeleti API használatával kapcsolatos információkért tekintse meg a [Storage-fiók létrehozása](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) referencia-témakör.
> 
> 

**A storage-fiók egy Azure-előfizetés létrehozása**

1. Jelentkezzen be a [a klasszikus Azure portálon].
2. Kattintson a bal alsó **új**. Az a **új** párbeszédpanelen válassza **adatszolgáltatások**, majd kattintson a **tárolási**, majd **Gyorslétrehozás**.
   
   A **Storage-fiók létrehozása** párbeszédpanel jelenik meg.
   
   ![Storage-fiók létrehozása][create-new-storage-account]
3. Az a **URL-cím** mezőbe írja be egy altartomány nevét. Ez a bejegyzés a 3-24 kisbetűket és számokat tartalmazhat.
   
    Ez az érték lesz az állomásnév, az előfizetés Blob, sor vagy tábla erőforrásainak címzéséhez használt URI-Azonosítóra belül. Egy tároló-erőforrás a Blob szolgáltatás megoldására használhatja egy URI-t a következő formátumban, ahol  *&lt;StorageAccountLabel&gt;*  hivatkozik a beírt érték **adjon meg egy URL-cím**:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **Fontos:** az URL-cím címke űrlapok a tárfiók URI altartomány, és az Azure-ban az összes üzemeltetett szolgáltatások egyedinek kell lennie.
   
    Ezt az értéket is használja a tárfiók a portálon, vagy neveként ezt a fiókot programozott módon való hozzáférés során.
4. Az a **régiót vagy Affinitáscsoportot** legördülő listában válassza a tárfiók csoport olyan régióban vagy a kapcsolat. Az affinitáscsoportokban helyett egy régiót akkor válassza, ha azt szeretné, hogy a tárolási szolgáltatások más Windows Azure-szolgáltatásokkal, Ön által használt azonos adatközpontba találhatók. Javíthatja a teljesítményt, és nem díjak sem merülnek fel a kimenő forgalom.  
   
   **Megjegyzés:** affinitáscsoport létrehozásához nyissa meg a **beállítások** a kezelési portál területen kattintson a **Affinitáscsoportok**, ezután kattintson **affinitáscsoporthozzáadása** vagy **hozzáadása**. Is létrehozása és kezelése a Windows Azure szolgáltatásfelügyeleti API használatával affinitáscsoportokat. További információ: [műveletek affinitáscsoportokkal].
5. Az a **előfizetés** legördülő listára, válassza ki az előfizetést, a tárolási fiók használandó.
6. Kattintson a **Tárfiók létrehozása** lehetőségre. A storage-fiók létrehozása eltarthat néhány percet.
7. Győződjön meg arról, hogy a tárfiók sikeresen létrejött-e, ellenőrizze, hogy a fiók megjelenik-e a felsorolt elemeket **tárolási** állapotú **Online**.

<a id="Step2"> </a>

<h2>2. lépés: A tárfiók új CDN-végpont létrehozása</h2>

Ha a tárfiók CDN hozzáférésének engedélyezése vagy üzemeltetett szolgáltatásban futó, minden nyilvánosan elérhető objektumok jogosultak a CDN peremhálózati gyorsítótár. Ha módosítja a CDN jelenleg gyorsítótárazott objektumhoz, az új tartalom nem lesz elérhető a CDN mindaddig, amíg a CDN tartalmát frissíti, a gyorsítótárazott tartalom idő a működés közbeni időszak lejártával.

**A tárfiók új CDN-végpont létrehozása**

1. Az a [a klasszikus Azure portálon], kattintson a navigációs ablaktáblában **CDN**.
2. Kattintson a menüszalagon **új**. Az a **új** párbeszédablakban válassza **alkalmazásszolgáltatások**, majd **CDN**, majd **Gyorslétrehozás**.
3. Az a **forrástartományt** legördülő menüben válassza ki a tárolási fiók a rendelkezésre álló tárfiókok a listán az előző szakaszban létrehozott. 
4. Kattintson a **létrehozása** gombra az új végpont létrehozásához.
5. Miután létrehozta a végpontot, az előfizetés végpontjainak listájában jelenik meg. A listanézetben látható a gyorsítótárazott tartalom eléréséhez használható URL-cím, valamint a forrástartomány is. 
   
    A forrástartomány az a hely, ahonnan a CDN a tartalmakat gyorsítótárazza. Lehet, hogy a forrástartomány vagy egy tárfiókot, vagy egy felhőalapú szolgáltatás; a storage-fiók ebben a példában a célokat szolgál. Tárolási tartalom gyorsítótárazva van, a peremhálózati kiszolgálóinak alapján történik a cache-control beállítást, amely megadja, vagy az alapértelmezett heurisztikus a gyorsítótárazási hálózat. 

    > [AZURE.NOTE]A végpont létrehozott konfigurációs nem azonnal elérhetők; a regisztráció CDN hálózati propagálásához legfeljebb 60 percig is tarthat. Felhasználók azonnali használatát, a CDN-tartománynevet próbáló állapotkód: 400 (hibás kérés) jelenhet meg addig, amíg a tartalom nem érhető el a CDN keresztül.

<a id="Step3"> </a>

<h2>3. lépés: Hozzáférési CDN-tartalom</h2> 

A CDN a gyorsítótárazott tartalom eléréséhez a CDN a portálon megadott URL-CÍMÉT használja. A gyorsítótárazott blob címet a következőhöz hasonló lesz:

http://<*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*Blobnév*\>

<a id="Step4"> </a>

<h2>4. lépés: A tartalom eltávolítása a CDN-t</h2>

Ha már nem szeretne gyorsítótárazása az objektum az az Azure Content Delivery Network (CDN), akkor is igénybe vehet az alábbi lépések egyikét:

* Egy Azure BLOB törölheti a blob a nyilvános tárolóból.
* Biztosíthatja, hogy a tároló privát nyilvános helyett. Lásd: [hozzáférés korlátozása a tárolók és Blobok](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) további információt.
* Tiltsa le, vagy törölni a CDN-végpontot a felügyeleti portál használatával.
* Az üzemeltetett szolgáltatás nem válaszol a kérelmekre a objektum módosíthatja.

Az objektum már gyorsítótárazza a CDN gyorsítótárazott marad, amíg az objektum idő a működés közbeni időszakának lejártáig támogatja. Az idő a működés közbeni időszak lejártával a CDN ellenőrzi, hogy a CDN-végpont továbbra is érvényes, és az objektum névtelenül továbbra is elérhetők maradnak. Ha nem, majd az objektum rendszer már nem gyorsítótárazható.

Közvetlenül a tartalom kiürítése lehetőséget jelenleg nem támogatott az Azure felügyeleti portálon. Lépjen kapcsolatba a [az Azure támogatási](https://azure.microsoft.com/support/options/) Ha szeretné azonnal törli a tartalmat. 

## <a name="additional-resources"></a>További források
* [Az Affinitáscsoportokban létrehozása az Azure-ban]
* [Hogyan: Storage-fiókok Azure-előfizetések kezelése]
* [A szolgáltatáskezelő API kapcsolatos]
* [CDN-tartalom leképezése egyéni tartományra]

[Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[Azure CDN-csomóponti helyek]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
[a klasszikus Azure portálon]: https://manage.windowsazure.com/
[számlázási csomag]: /pricing/calculator/?scenario=full
[Az Affinitáscsoportokban létrehozása az Azure-ban]: http://msdn.microsoft.com/library/azure/ee460798.aspx
[Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
[A szolgáltatáskezelő API kapcsolatos]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
[CDN-tartalom leképezése egyéni tartományra]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
