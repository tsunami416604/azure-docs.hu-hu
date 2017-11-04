## <a name="create-a-storage-account-using-the-azure-portal"></a>Hozzon létre egy tárfiókot, az Azure portál használatával

Először hozzon létre egy új általános célú tárfiókok fiókot szeretné használni a gyors üzembe helyezés. 

1. Lépjen a [Azure-portálon](https://portal.azure.com) , és jelentkezzen be Azure-fiókjával. 
2. A központ menüben válassza ki a **új** > **tárolási** > **tárfiók - blob, a fájl, a tábla, a várólista**. 
3. Adjon meg egy egyedi nevet a tárfiók. Ezek a szabályok tartsa szem előtt a tárfiók elnevezési:
    - A név 3 – 24 karakter hosszúságúnak kell lennie.
    - A név számokat és csak kisbetűket tartalmazhat.
4. Győződjön meg arról, hogy a következő alapértelmezett értékek vannak-e beállítva: 
    - **Telepítési modell** értéke **erőforrás-kezelő**.
    - **Fiók kind** értéke **általános célú**.
    - **Teljesítmény** értéke **szabványos**.
    - **Replikációs** értéke **helyileg redundáns tárolás (LRS)**.
5. Válassza ki előfizetését. 
6. A **erőforráscsoport**, hozzon létre egy újat, és adjon neki egy egyedi nevet. 
7. Válassza ki a **hely** a tárfiók használatára.
8. Ellenőrizze **rögzítés az irányítópulton** kattintson **létrehozása** a tárfiók létrehozásához. 

A tárfiók létrehozása után az irányítópulton van rögzítve. Kattintson rá a megnyitásához. A **beállítások**, kattintson a **hívóbetűk**. Válassza ki az elsődleges kulcs, és másolja a társított **kapcsolati karakterlánc** a vágólapra, majd illessze be egy szövegszerkesztőben későbbi használatra.
