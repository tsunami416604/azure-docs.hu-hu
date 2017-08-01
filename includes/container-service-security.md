# <a name="securing-docker-containers-in-azure-container-service"></a>Docker-tárolók védelme az Azure Container Service-ben

Ez a cikk az Azure Container Service-ben üzembe helyezett Docker-tárolók védelmével kapcsolatos szempontokat és javaslatokat tartalmazza. A szempontok nagy része általánosságban az Azure vagy egyéb környezetben üzembe helyezett Docker-tárolókra vonatkozik. 

## <a name="image-security"></a>Rendszerképek biztonsága

A tárolók egy vagy több adattárban található rendszerképekből állnak össze. Az adattárak nyilvános vagy privát tárolójegyzékekhez tartozhatnak. Ilyen nyilvános beállításjegyzék például a [Docker Hub](https://hub.docker.com/). Privát beállításjegyzék pedig például a [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), amely a helyszínen vagy virtuális magánfelhőbe is telepíthető. Léteznek továbbá felhőalapú privát tárolójegyzék-szolgáltatások is, mint az [Azure Container Registry](../articles/container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Nyilvános és privát rendszerképek
Általánosságban, ahogyan más nyilvánosan közzétett szoftvercsomagok, a nyilvánosan elérhető tárolórendszerképek sem garantálják a biztonságot. A tárolórendszerképek több szoftverrétegből épülnek fel, és mindegyik rétegben lehetnek biztonsági rések. Fontos megismerni a tárolórendszerkép eredetét, beleértve a rendszerkép tulajdonosát (így megállapítható, hogy a forrás megbízható-e), a rétegeit és a szoftververziókat. 

Például a Docker Hubon nyissa meg a hivatalos [nginx adattárat](https://hub.docker.com/_/nginx/), és lépjen a **Címkék** lapra. Itt színkódokkal vannak jelölve az egyes rendszerképek biztonsági rései. Minden egyes szín a rendszerkép valamely szoftverrétegének egy biztonsági rését jelöli. További információt a biztonsági rések a Docker Hubon való vizsgálatával kapcsolatban a [Docker Hub hivatalos adattárait ismertető](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/) cikkben talál.

![Nginx-rendszerképek a Docker Hubon](./media/container-service-security/docker-hub-nginx.png)

A vállalatok számára rendkívül fontos a biztonság, és annak érdekében, hogy védve legyenek a biztonsági támadásokkal szemben, a rendszerképeket érdemes privát beállításjegyzékekből lekérniük és tárolniuk, mint az Azure Container Registry vagy a Docker Trusted Registry. Az Azure Container Registry egy felügyelt privát beállításjegyzéket kínál, és támogatja az [egyszerűszolgáltatás-alapú hitelesítést](../articles/container-registry/container-registry-authentication.md) az Azure Active Directoryn keresztül az alapszintű hitelesítési folyamatok esetében, beleértve a szerepköralapú hozzáférést a csak olvasási, írási és tulajdonosi engedélyekhez.

### <a name="image-security-scanning"></a>Rendszerképek biztonsági ellenőrzése

Még privát beállításjegyzékek használata esetén is érdemes rendszerkép-ellenőrzési megoldásokat alkalmazni további biztonsági ellenőrzés céljából. A tárolórendszerképek minden egyes szoftverrétegében az adott tárolórendszerkép többi rétegétől függetlenül potenciálisan előfordulhatnak biztonsági rések. A vállalatok egyre inkább tárolótechnológiákra alapozzák éles számítási feladataikat, emiatt pedig a rendszerképek ellenőrzése is egyre fontosabb a szervezetek elleni biztonsági fenyegetések megelőzése érdekében. 

A biztonsági monitorozási és ellenőrzési megoldásokkal, többek között a [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) és az [Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) segítségével ellenőrizhetőek a privát beállításjegyzékben lévő tárolórendszerképek, és azonosíthatóak a potenciális biztonsági rések. Fontos tudni, hogy a különféle megoldások milyen részletességű ellenőrzést biztosítanak. Egyes megoldások például csak az ismert biztonsági résekkel szemben ellenőrzik a rendszerképek rétegeit. Előfordulhat, hogy az ilyen megoldások nem képesek ellenőrizni egyes csomagkezelő szoftverekkel készített rendszerképréteg-szoftvereket. Más megoldások azonban részletesebb ellenőrzésre képesek, és bármely csomagolt szoftverben képesek megtalálni a biztonsági réseket.

### <a name="production-deployment-rules-and-audit"></a>Éles rendszerek üzembe helyezésének szabályai és ellenőrzése
Alkalmazások éles környezetben való üzembe helyezésekor fontos beállítani néhány szabályt annak érdekében, hogy az éles környezetben használt rendszerképek biztonságosak legyenek, és ne tartalmazzanak biztonsági réseket.

* Alapszabályként elmondható, hogy ha egy rendszerkép biztonsági rést tartalmaz, nem szabad éles környezetben futtatni, még akkor sem, ha a biztonsági rés kicsi. Emellett ideális esetben az összes, éles környezetben üzembe helyezett rendszerképet olyan privát beállításjegyzékbe érdemes menteni, amelyhez csak kevesen férnek hozzá. Fontos továbbá minél kevesebb éles rendszerképet használni, hogy hatékonyan lehessen őket kezelni.

* Mivel a nyilvánosan elérhető tárolórendszerképekben lévő szoftverek származása nehezen deríthető ki, célszerű a rendszerképeket a forrásból felépíteni, hogy ismerjük a réteg eredetét. Ha a saját készítésű tárolórendszerképben jelentkezik valamilyen biztonsági rés, könnyebben található rá megoldás. Nyilvános rendszerképek javításához a felhasználóknak fel kell kutatniuk azok gyökerét, vagy be kell szerezniük egy másik, biztonságos rendszerképet a közzétevőtől.

* Nem garantált, hogy az éles környezetben üzembe helyezett, alaposan ellenőrzött rendszerképek az alkalmazás teljes élettartama során végig naprakészek maradnak. Biztonsági rések jelenhetnek meg a rendszerkép korábban nem ismert vagy az éles környezetben való üzembe helyezés után bevezetett rétegeiben. Az éles környezetben üzembe helyezett rendszerképeket rendszeresen ellenőrizni kell az elavult vagy egy ideje már nem frissített rendszerképek azonosítása érdekében. A tárolórendszerképek állásidő nélküli frissítése párhuzamos (kék-zöld) üzembe helyezéssel és működés közbeni frissítési mechanizmusokkal valósítható meg. A rendszerképek ellenőrzése az előző szakaszban ismertetett eszközökkel végezhető el. 

* A rendszerképek készítését és integrált biztonsági ellenőrzését magukban foglaló folyamatos integrációs (CI) folyamatokkal megvalósítható a biztonságos tárolórendszerképeket tartalmazó biztonságos privát beállításjegyzékek fenntartása. A CI megoldásba beépített biztonságirés-ellenőrzési funkció biztosítja, hogy az összes teszten megfelelő rendszerképek kerüljenek a privát beállításjegyzékbe, ahonnan az éles számítási feladatok üzembe helyezése történik. Ha a CI folyamat meghiúsul, akkor a biztonsági résekkel rendelkező rendszerképek nem kerülnek az éles számítási feladatok üzembe helyezéséhez használt privát beállításjegyzékbe. Emellett a folyamattal automatizálható a rendszerképek biztonsági ellenőrzése, ha sok rendszerkép van használatban. Máskülönben a rendszerképek manuális vizsgálata rendkívül hosszadalmas és sok hibalehetőséget tartalmazó folyamat lenne.

## <a name="host-level-container-isolation"></a>Tárolók gazdagépszintű elkülönítése
Ha a felhasználók Azure-erőforrásokon helyeznek üzembe tárolóalkalmazásokat, azok az előfizetés szintjén lesznek üzembe helyezve erőforráscsoportokban, és nem lesznek több-bérlősek. Ez azt jelenti, hogy ha a felhasználók másokkal közös előfizetést használnak, nem lehetnek határok egy adott előfizetés két telepítése között. Emiatt a tárolószintű biztonság nem biztosítható. 

Fontos tudni továbbá, hogy a tárolók osztoznak a gazdagép kernelén és erőforrásain (az Azure Container Service-ben a gazdagép egy Azure-beli virtuális gép egy fürtben). Az éles környezetekben futó tárolókat ezért nem kiemelt jogosultságú felhasználói módban kell futtatni. Ha valamely tárolót gyökérszintű jogosultságokkal futtatja, azzal veszélynek teheti ki a teljes környezetet. Ha a támadó gyökérszintű hozzáféréssel rendelkezik egy tárolóhoz, megszerezheti a gazdagép teljes gyökérszintű jogosultságait. Emellett az is fontos, hogy a tárolókat csak olvasható fájlrendszerekkel futtassa. Ez meggátolja, hogy a tárolóhoz hozzáféréssel rendelkező személyek rosszindulatú szkripteket írhassanak a fájlrendszerre, és hozzáférést szerezzenek más fájlokhoz. Hasonlóképpen fontos korlátozni a tárolóhoz rendelt erőforrásokat (például a memóriát, a processzorokat és a hálózati sávszélességet). Így a támadók nem tudják kisajátítani az erőforrásokat, és azokon keresztül illegális tevékenységeket, például hitelkártyacsalást vagy bitcoinbányászatot végezni, ami megakadályozhatná más tárolók ugyanezen a gazdagépen vagy fürtben való futtatását.

## <a name="orchestrator-considerations"></a>A vezénylőkkel kapcsolatos szempontok

Az Azure Container Service-ben elérhető egyes vezénylőkre más-más biztonsági szempontok vonatkoznak. Például érdemes korlátozni a Container Service-ben futó vezénylő-csomópontokhoz való közvetlen SSH-hozzáférést. Ehelyett inkább az egyes vezénylők felhasználói felületét vagy parancssori eszközeit érdemes használni a tárolókörnyezet kezeléséhez (ilyen például a `kubectl` a Kubernetes esetében) a gazdagépek elérése nélkül. További információkért lásd: [Távoli kapcsolat létrehozása egy Kubernetes-, DC/OS- vagy Docker Swarm-fürttel](../articles/container-service/kubernetes/container-service-connect.md).

Az egyes vezénylőkre vonatkozó további biztonsági információkat a következő forrásanyagokban talál:

* **Kubernetes**: [Ajánlott biztonsági eljárások a Kubernetes-telepítéshez](http://blog.kubernetes.io/2016/08/security-best-practices-kubernetes-deployment.html)

* **DC/OS**: [A fürt védelmének biztosítása](https://dcos.io/docs/1.8/administration/securing-your-cluster/)

* **Docker Swarm**: [Docker-biztonság](https://www.docker.com/docker-security)

## <a name="next-steps"></a>Következő lépések

* A Docker architektúrával és tárolók biztonságával kapcsolatos további információkért lásd: [a tárolóbiztonságot bemutató](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf) dokumentumot.

* Az Azure-platform biztonságáról az [Azure Security Centerben](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure) találhat információkat.