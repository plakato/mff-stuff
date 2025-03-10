# 1. Základy složitosti a vyčíslitelnosti
- Výpočetní modely (Turingovy stroje, RAM). 
- Rozhodnutelné a částečně rozhodnutelné problémy. 
- Algoritmicky nerozhodnutelné problémy (halting problem). 
- Nedeterministický výpočetní model. 
- Základní třídy složitosti a jejich vztahy. 
- Věty o hierarchii. 
- Úplné problémy pro třídu NP, Cook-Levinova věta. 
- Pseudopolynomiální algoritmy, silná NP-úplnost. Aproximační algoritmy a schémata.

# Výpočetní modely (Turingovy stroje, RAM).
## Turingův stroj 
- (Q, E, d, q0, F) - Q: stavy, E: abeceda (včetně prázdného znaku), d: QxE->QxEx{R,N,L} přechodová funkce, q0 z Q: počáteční stav, F ⊆ Q: přijímací stavy
- **konfigurace: (stav, slova na pásce, pozice hlavy)**
- **displej** dvojica (q, a) - stav, znak
- začiatok: na pásce vstup, mimo něj prázdné znaky, hlava na prvním políčku, ... 
- podle aktuálního znaku a stavu se vybere přechodová funkce, zapíše se její result na aktuální políčko, změní stav, a přesune se Left, Right, Nowhere
    - pokud není validní přechod - výpočet končí
- TS M
    - TS M **přijímá** slovo w: výpočet končí a po ukončení se M nachází v přijímacím stavu 
        - množina přijímaných slov: L(M)
    - TS M **odmítá** slovo w: výpočet končí a po ukončení se M nenachází v přijímacím stavu 
    - TS M **konverguje** nad w: výpočet končí 
    - TS M **diverguje** nad w: výpočet nekončí 
    - **Turingovsky vyčíslitelné (retezcové) funkce**
        - f_M, funkce, kt. TS M počíta
        - doména = reťazce, nad ktorými sa zastaví
        - M(w) - pre každé slowo w z domény výpočet skončí, že na výstupnej páske je slovo f_M(w)
        - funkce f: je turingovsky vyčíslitelná pokud existuje TS M,který ji počítá 
            - každá tur. vyčíslitelná funkce má nekonečně strojů, které ji počítají 
- varianty TS:
    - jednosměrné s nekonečnou páskou
    - více páskami 
    - více hlavami na více paskách (spojené hlavy/nezávislé)
    - pouze binární abecedou 
    - nedeterministické TS   
    -> všechny jsou ekvivalentní našemu TS: přijímají stejnou třídu jazyků a vyčislují tutež třídu funkcí


### k-páskový TS
- každá páska má hlavu zvlášť 
- vstupní páska: vstup, jen čtení
- výstupní páska: výstup, prázdná na začátku, jen zápis 
- pomocní pásky: prázdné na začátku, read/write 
-  `Q x E^k -> Q x E^k  x  {R,N,L}^k`
- ekvivalentní jednopáskovému
    - jednopáskový je špeciálny prípad viacpáskového
    - ukážme, že výpočet k-páskového ide simulovať jednopáskovým
        - pásky umiestnime za seba, oddelíme špec. znakom - jedna páska 
        - pozice hlav označujeme speciálním znakem (tj. v každom úseku "minipásky" je jedna značka)
        - ak chcem predĺžiť nejakú pásku, tak to celé musím posunúť
        - nová abeceda: kartézský součin původních k abeced + pozice hlav 

## RAM 
- výpočetní stroj s náhodným přístupem do paměti
- neomezená paměť "pole" (neobmedzená postupnosť registrov)
- oddelená pamäť pre dáta a program (Harvardská architektúra)
- **LOAD, ADD, SUB, COPY, JNZ** (jump not zero), **READ, PRINT**
    - přímá/nepřímá adresace 
    - load načte 0 -> konec vstupu 
- **RAM přijme**: ukončí a první číslo které zapíše na výstup je 1 
- **RAM odmítne**: nic, nebo jiné než 1
- ak pre jazyk L existuje RAM R, L=L(R), tak je **částečne rozhodnutelný RAMem**
    - nad každým vstupem sa zastaví = **rozhodnutelný RAMem**
- **RAM vyčísliteľná funkcia**
    - necht f: N^n -> N je částečná funkce (nemusí byť definovaná pre vŠetky vstupy)
    - RAM R počíta f, ak platí:
        - ak nie je definovaná, tak RAM neskončí, alebo nikdy nič nevypíše
        - ak je definovaná, zapíše sa aspoň jedna hodnota, kde prvá je výsledok funkcie

### Ekvivalence RAM a TS: 
- TS->RAM 
    - obsah pásky uložen ve dvou polích `Tl, Tr` (levá a pravá část pásky)
    - index polohy hlavy v proměnné `h`, stav v proměnné `q`, strana pásky `s`
    - výběr instrukce: podle `h`, `q`, `s`
        - ~velký switch na odpovící branch přechodové funkce 
- RAM->TS: 
    - čtyřpáskový TS
    - súčasťou stavu je program counter
    - každá inštrukcia RAMu je nahradená reťazcom inštrukcií TS
        - končí s program counter na Ij+1 po Ij tej operácii
    - vstupní páska: data, co má RAM dostat na vstupu 
        - binárně zapsaná čísla
        - oddělení #
    - výstupní páska: ekvivalent, výstup RAMu, oddelené # 
    - paměť RAM: obsah paměti 
        - `(index registru) | (obsah registru) # (index registru) | (obsah registru) | ...`
        - binárne kódujeme indexy aj obsahy
    - pomocná páska: mezivýpočty, nepriame adresy, posuny pasok... 
    
#### Churchova-Turingova teze
- ke každému algoritmu v intuitivním smyslu existuje ekvivalentní Turingův stroj 

## Gödelovo číslo
- chceme TS přiřadit číslo 
- mějme TS M s jediným přijímacím stavem, binární vstupní abecedou (každý TS lze převést do této podoby)
- počiatočný stav `q0`, prijímací `q1`
- potřeba zakodovat přechodovou funkci: 
    - abeceda: `0, 1, L, N, R, |, #, ; `
    - instrukce `d(qi, znak j) -> (qk, znak l, Z)` :: `i | j | k | l | Z` (binárne každú časť)
    - jednotlivé instrukce za sebou `C1#C2#C3...`
    - převedeme do binarního zapisu jednoznačně: 0->000, 1->001, L->010, ..., #->110, ...
- každému TS M lze přiřadit číslo
    - pokud číslo není validní zakodování, řekneme, že odpovídá prázdnému TS, který vše odmítne 
        -> každému TS M odpovídá číslo, každému číslu TS M 
- kód TS není jednoznačný (pořadí např. instrukcí)

## Universální TS
- vstupem U je dvojice: <M, x>, kde M je TS a x řetězec 
    - U simuluje M na vstupe x 
- výstup je daný výstupom M(x) (aj zastavenie, prijatie, či zamietnutie)
- jazyk: universální jazyk Lu = {<M, x>| x ∈ L(M)}
- TS U: 3 páskový 
    - první páska vstup <M, x>
    - druhá páska obsah pracovní pásky M 
    - číslo stavu v ktorom je M

# Rozhodnutelné a částečně rozhodnutelné problémy   
- turingovsky rozhodnutelné jazyky
    - jazyk je rozhodnutelný (rekurzivní) pokud existuje TS M, který se **vždy zastaví a L=L(M)**
    - jazyk je částečně rozhodnutelný (rekurzivně spočetný) existuje-li M: L=L(M)    
- jsou li L1, L2 (částečně) rozhodnutelné jazyky
    - průniky, sjendocení, konkatenace, iterace jsou (částečně) rozhodnutelné jazyku
- **Postova věta**
    - **L rozhodnutelný <=> L a L' jsou částečně rozhodnutelné **
    - -> prohodím přijímající / nepřijímající stavy 
    - <- paralelní běh ML a ML', jeden z nich určitě jednou přijde -> můžu zastavit a přijmout/odmítnout
- vlastnosti částečně rozhodnutelných jazyků-
    - pro jazyk L ⊆ Σ* jsou násl. ekvivalence 
        - L je částečně rozhodnutelný 
        - existuje TS Me, který se právě pro každé x z L zastaví 
        - existuje **rozhodnutelný** jazyk B: L = {x ∈ Σ* | ∃ y ∈ Σ* | <x, y> ∈ B}
            - např. y reprezentuje počet kroků, mohu pak simulovat a po překročení odmítnout 
## Enumerátory
- pro jazyk L je to turingův stroj E 
    - ignoruje vstup 
    - během výpočtu vypisuje řetězce w z L 
    - každý řetězec z L je někdy vypsán
    - pokud je L nekonečný tak E nikdy neskončí 
- jazyk je **částečně rozhodnutelný <> existuje enumerátor E**
    - -> generuju slova lexikograficky do délky d, spouštím TS pro počet "k"roků, iterarivně obojí zvyšujeme   
    - <- čekám než iterátor vypíše slovo, pak přijmu
- jazyk je **rozhodnutelný <> existuje enumerátor E v pořadí od nejkratšího slova**
    - (sekundární kritérium třeba lexikografické, ale je to jedno) 
    - -> zkouším slova od nejkratšího, výpočet vždy skončí -> vypisuju když přijmou
    - <- počkám než vypíše slovo za aktuálním -> přijmu/nepřijmu

## Převoditelnost
- jazyk A je m-převoditelný na jazyk B pokud existuje `totální vyčíslitelná funkce` f
    - f: ∀x ∈ Σ* [x ∈ A <> f(x) ∈ B]
    - totálni vyč. f.: vycísliteľná funkcia, ktorá je všade definovaná
- jazyk A je m-úplný pokud je A částečně rozhodnutelný a každý částečně rozhodnutelný jazyk je na něj převoditelný 
    - Lu, Lhalt jsou m-úplné 

# Algoritmicky nerozhodnutelné problémy (halting problem)
- množství jazyků:
    - různých jazyků je nespočetně mnoho: abeceda Σ je konečná, Σ* (slova) je nekonečná spočetná (~odp. přirozeným číslům), P(Σ*) (jazyky) je nekonečná nespočetná (z Cantorovy věty)
    - částečně rozhodnutelných jazyků max spočetně
        - pro každý existuje TS M, TS lze zakodovat viz. Gödelovo číslo -> spočetně mnoho 
    - existuje nespočetně (i částečně) nerozhodnutelných jazků 

## Diagonalizační jazyk: není ani částečně rozhodnutelný
- `Ldiag = {<M> | <M> ∉ L(M)}`
- dk: sporem: nechť M_L přijímá Ldiag, pak <M_L> ∈ Ldiag <=> <M_L> ∉ Ldiag: 
    - řekněme, že <M_L> ∈ Ldiag -> M_L musí přijmout <M_L> -> dle definice spor 
    - řekněme, že <M_L> ∉ Ldiag -> přijme se -> musí být z jazyka -> spor 
- **Ldiag'** je částečně rozhodnutelný: skrze universální TS, díky Postově větě ale nemůže být rozhodnutelný 

## Universální jazyk je částečně rozhodnutelný, ale ne rozhodnutelný 
- je částečně rozhodnutelný neb existuje universální turingův stroj 
- kdyby byl rozhodnutelný, tak dle Postovy věty je Lu' částečně rozhodnutelný -> má TS M
    - Lu' = L(M) = {<M,x> | x ∉ L(M)}
    - pomocou stroje M sestrojime M', které by prijímal Ldiag
        - pokud vstup `e` nie je syntakticky správny TS, odmítnu (odpovedá stroju, čo vždy zamietne)
            - M'(e) teda prijme
        - pokud `e` je syntakticky správny zápis, tak M' spustí M pre <e, e>.
        - M' sa zachova ako výsledok M. Teda M' prijíma Ldiag, čo je spor
        
## Halting problem: jazyk všech <M, x> které zastaví je částečně rozhodnutelný ale ne rozhodnutelný
- Lhalt = {<M, x> | M(x) zastavi}
- částečná rozhodnutelnost plyne s existence universálního Mu (pro Lu)
- dk. sporom:
    - předpokládejme rozhodnutelnost -> Lhalt' je částečně rozhodnutelný (postova věta), má TS M, L(M) = Lhalt'
    - definujme L' = {<M> | M(<M>) nezastaví}
        - pomocou M, postavme M', ktorý čiastočne rozhoduje L'
            - ak je vstup nevalidny, zacyklí sa
            - inak spusti M(<e,e>)
                - ak prijme, prijmi
                - inak sa zacykli
            - M' prijme svoj vstup, práve vtedy, keď sa zastaví
        - zrejme platí, L(M') = L'
        - Lhalt' = L'
        - -> pokud <M'> z L' ->  M' se na <M'> musí zastavit -> z definice L' se M'(<M'>) nemá zastavit -> spor 
        - -> pokud <M'> !z L' ->  podle definice L' M'(<M'>) zastaví -> dle popsání M' ale přijme, z čehož vyplývá <M'> z L' -> spor 
    - ... alias... vytvorme program, čo zavolá halt na vstup a robí presný opak toho, čo halt povie. Čo sa stane, keď pre tento program dáme samého seba? Spýta sa halt, či sa zastaví, ak sa zastaví, tak sa zacyklí. Obdobne druhá situácia, čo dáva spor (halt nemôže existovať)

## Riceova věta 
- nechť C je třída částečně rozhodnutelných jazyků, potom Lc = {<M> | L(M) ∈ C}, pak Lc je rozhodnutelný právě pokud je C triviální  (vše/nic)
- spor: mějme netriviální třídu C, Mc je TS pro Lc, který (z rozhodovatelnsoti) vždy konverguje 
- zvolme jazyk z C přijímaný TS N
- vytvořme TS M, který na vstup pustí vlastní kód <M> a pokud se zastaví, tak spustí stroj N s původním vstupem 
    - pokud M(<M>) konverguje -> L(M) == L(N)
                                -> <M> z Lc
    - pokud M(<M>) diverguje -> <M> !z Lc 
    -> pomocí Mc ověřím jestli Mc(<M>), tj. jestli je <M> z Lc, čímž rozhodnu jestli <M> neupravený haltuje 
    -> dá se rozhodovat jazyk K = <M> | M(<M>) zastaví -> spor protože je nerozhodnutelný

# Nedeterministický výpočetní model
- nedeterministický TS: d: QxE -> P(QxΣx{L, N, R})
    - NTS M v každém kroku uhodne/vybere správnou instrukci 
    - NTS M vykoná všechny možné instrukce současně / je ve všech konfiguracích najednou 
- stejná výpočetná sila jako normální TS ~ stejně silný w.r.t to Churchov-Turing teze 
    - kartezsky soucin stavu
    - iterative deepening simulace 
- výpočet NTS M 
    - posloupnost konfigurací C0, C1, ... Cn 
    - přechody jen možné, přijímající pokud konečný & stav příjímající, ... 
- NTIME(f(n)), NSPACE(f(n)) - nad každým vstupom, každý výpočet skončí v O(f(n)) krokoch (použije max O(f(n)) pásky)
- NP = U_{k in N} NTIME(n^k)
    - trieda jazykov prijímaná nedeterministickými TS v polynomiálnom čase

## Verifikátor 
- verifikátor pro jazyk L je algoritmus V: L = {x| ∃y V prijme (x, y)}
    - polynomiální verifikátor: pracuje v polynomiálním čase vůči |x|
    - pokud má y pol(x) délku: polynomiální certifikát 
- NP: třída jazyků s polynomiálními verifikátory 

### Ekvivalence definic NP
- -> existuje poly. verif: 
    - NTS Ml první nedeterministicky uháhne 'y' 
        - lze, prostě má přechody pro každý možný znak, lineárně uhádne celý
    - odsimiluje dle předpokladů existující Mb pro funkci B 
- <- jako 'y' můžeme brát jakou ze všech možných (nedeterministicky zvolených) předodů NTM vybere 
    - maximálně polynomálně moc kroků -> y max polynomiálně dlouhé 

# Základní třídy složitosti a jejich vztahy
- typy problému:
    - rozhodovací problémy: ptáme se zda instance x má nějakou vlastnost, jazyk kladných instancí L <= E*, query: x z L 
    - úloha pro danou instanci x: hledáme y, které splňuje určitou podmínku -> hledání y z relace R <= E*xE* 
    - optimalizační úloha: hledáme takové y, které minimalizuje externí míru 

## Časová a prostorová složitost 
- nechť M je TS M, f: N->N funkce 
- **M pracuje v čase f(n)**: každý výpočet M nad libovolným vstupem x |x|=n skončí po provedení nejvýše f(n) kroků 
- **M pracuje v prostoru f(n)**: každý výpočet M nad libovolným vstupem x |x|=n využije maximálně f(n) buněk pracovní pásky 
- **NTIME(f(n))**: třída jazyků přijímaných nedetermministickým TS, které pracují v čase O(f(n))
- **TIME(f(n))**: třída jazyků přijímaných determministickým TS, které pracují v čase O(f(n))
- **NSPACE(f(n))**: třída jazyků přijímaných nedetermministickým TS, které pracují v paměti O(f(n))
- **SPACE(f(n))**: třída jazyků přijímaných determministickým TS, které pracují v paměti O(f(n))
- **NP**: U_k∈N NTIME(n^k) : přijímané nedeterministickým turing. v polynomiálním čase
- **P**: U_k∈N TIME(n^k) : přijímané deterministickým turing. v polynomiálním čase 
- **EXPTIME**: U_k∈N TIME(2^n^k)
- modely s menším než lineráním space/time 
    - potřeba mít více pásek: vstupní, výstupní, pracovní  
    - do času a prostoru pouze pracovní páska 
    - konfigurace neobsahuje vstupní slovo 
- **L**: SPACE(log2n)
- **NL**: NSPACE(log2n)
- **NPSPACE**: Uk NSPACE(n^k)
- pro každou f: platí, že 
    - TIME(f(n)) ⊆ SPACE(f(n)): 
        - nelze popsat víc buněk než kolik je času (sublineární: třeba nepočítat vstup, viz výše)
    - TIME(f(n)) ⊆ **NTIME(f(n)) ⊆ SPACE(f(n))** ⊆ NSPACE(f(n))
        - druhá ⊆: simulujeme, max const možností/paměti per krok, tj. f(n)*const paměti pro backtrack záznam 
- **f(n) funkce f(n) >= log(n), pro každý L z NSPACE(f(n)) existuje L z TIME(2^(cl * f(n)))**
    - počet konfigurací je omezen 2^(cl * f(n)) -> odhad času (přijímací výpočet se v každé konfiguraci ocitne jen jednou else zacyklení)
- platí inkluze: L ⊆ P ⊆ NP ⊆ PSPACE ⊆ NPSACE ⊆ EXPTIME 

### Polynomiální třídy
- nerostou příliš rychle 
- jsou uzavřené na skládání 
- silnější Church-Turing: každý rozumný a obecný výpočet lze na TS simulovat s polynomiálním zpomalením/zvětšením potřebného prostoru  
    - P/PSPACE nezávislé na zvoleném výpočetním modelu / prog. jazyce 

## Savičova věta: pro každou f(n) >= log2n: NSPACE(f(n)) ⊆ SPACE(f^2(n))
- počet konfigurací je omezen 2^(cl * f(n))
- skrz dynamické progrmaování a půlení (vždy zafixujeme mid-bod, hledáme cestu k němu a od něj) procházíme grafem (pouze unikátní konfigurace) všech možných konfigurací
    - v každém volání zafixujeme/otestujeme 1 konfiguraci a zavoláme se na 2 pod-problémy -> celkem 2^f(n) zafixovaných bodů 
    - hloubka rekurze: O(f(n)) 
    - jedna instance O(f(n))
    -> O(f^2(n))
- dôsl.: PSPACE = NPSPACE 

# Věty o hierarchii
- f: N->N f(n)>logn je **prostorově konstruovatelná**, pokud zobrazuje **1^n** na **binární representaci f(n)** a je **vyčíslitelná v prostoru O(f(n))**
    - zo vstupu 10 jednotiek, mám vypísať binárny zápis 11, ak f je +1, takže výsledok 1011 (nesmiem presiahnut prestor O(10))
- f **vyčíslitelná v prostoru O(f(n))**: existuje TS, který pracuje v prostoru O(f(n)) na vstupem 1^n a po ukončení je zapsán řetězec 1^f(n) 
    - ~odpovídá zarezervování prostoru, není každá funkce, většina ano 

## Věta o deterministické prostorové hierarchii 
- **pro každou prostorově konstruovatelnou funkci f: ∃ L rozhodnutelný v prostoru O(f(n)) ale ne o(f(n))**
- dôkaz konštrukciou
    - L = <M>, 10^k | M odmítá <<M>, 10^k> v prostoru <= f(|<<M>, 10^k>|)
    - rozhodnutelné v prostoru O(f(n))
        - pro vstup x, |x| = n, spocti f(n), označ dost buněk na pásce, pokud alg. zkusí označit víc buněk -> odmítni
        - pokud x není validního tvaru, odmítni 
        - simuluj běh M na x, pokud přesáhne f(n) prostoru nebo 2^f(n) času (viz odhad konfigurací, řeší zacyklení, na počítání času stačí log(2^fn)) == fn prostoru, odmítni 
        - pokud M přijme, odmítni, jinak přijmi 
    - L je nerozhonutelný v prostoru o(f(n)): sporem, mějme TS M rozhoduje L, slovo w <<M>, 10^k>
        - w z L <> M přijme w <> M nepřijímá <<M>, 10^k> v prostoru <= f(|<<M>, 10^k>|) <> spor s předpokladem že `M přijme w` `v prostoru o(f(n))`
        - uplatňuje se 10^k, kdyby tam nebylo tak velikost vstupu pouze |M|, celá práce se musí vejít do O(|M|)
        - 10^k nám poskytuje prostor pro práci -> zajišťuje, že odmítnutí není kvůli překročení prostoru z důvodu simulace (tj. kde by M nativně nepřekročilo)
- důsledky
    - jsou li f1, f2; f1 z o(f2); f2 prostorově konstruovatelná: **SPACE(f1) ⊂ SPACE(f2)**
    - e1 < e2 : **SPACE(n^e1) ⊂ SPACE(n^e2)**
    - **NL ⊂ PSPACE ⊂ EXPSPACE**

## Věta o deterministické časové hierarchii 
- **pro každou časově. konstr. funkci f existuje jazyk A, rozhonutelný v O(f(n)) ale ne o(f(n)/log(f(n)))**
- faktor log(f(n)) navíc neb TS simulující důkaz si musí pamatovat konfiguraci stroje, který simuluje  
- dôkaz konštrukciou
    - L = <M>, 10^k | M neprijima <M>, 10^k v case <= f(n) / log(f(n))
    - L rozhodnutelný v čase O(f(n)):
        - pro vstup x spočti f(n)
        - pokud x není v korektním tvaru, odmítni 
        - Simuluj M na vstupu x, pokud nedoběhne v f(n) / log(f(n)) krocích, odmítni 
        - Pokud přijme odmítni, jinak přijmi

        - každý krok je třeba snížit čítač: v každém kroku simulace max O(log(f(n))) práce (max délka) -> náročnost simulace je O(f(n))
    - L není rozhodnutelný v o(f(n) / log(f(n)))
        - nechť je v daném čase rozhodnutelný pomocí TS M, slovo w = <M>, 10^k 
        - w z L <> w přijmuté M v `o(f(n) / log(f(n)))` <> spor s definicí `M neprijima <M>, 10^k v case <= f(n) / log(f(n))`
- důsledky
    - jsou li f1, f2; f1 z o(f2/logf2n); f2 prostorově konstruovatelná: **TIME(f1) ⊂ TIME(f2)**
    - **TIME(n^e1) < TIME(n^e2)**
    - **P < EXPTIME**

# Úplné problémy pro třídu NP, Cook-Levinova věta
- polynomiální převoditelnost 
    - A je převoditelný v polyn. čase na B `A <= B` pokud E f: E*->E* vyčíslitelná v polynomiálním čase pro kterou platí `V w z E* (w z A <> f(w) z B)` 
    - vlasnosti: 
        - <= reflexivní a transitivní relace 
        - pokud A <= B a B z P, tak A z P 
        - pokud A <= B a B z NP, tak A z NP 
- **NP-těžký**: je na něj převoditelný každý problém z A z NP 
- **NP-úplný**: je-li NP těžký a ještě z NP 
    - za předpokladu P != NP : pokud B NP úplný -> B !z P 

## Cook-levinova věta 
- **Pokud by byl SAT řešitelný v polynomiálním čase, pak by P = NP.**
- Respektive, SAT je NP úplný problém.

- Dokážeme, že kachlíkování je NP úplné: 
    - množina barev B, čtvercova siť s okrajema baravenýma, kachlíky <= B^4, kachlíky se mohou opakovat 
    - existuje přípustné vykachlíkování takové, kde sedí barvy na krajích a dotýkající se barvy sousedních kachlíků?

    - KACHL z NP: dostaneme-li okachlikovani (certifikát) umíme v polz čase ověřit, jestli je přípustné 
    - nechť A náleží NP, existuje k němu NTS M s počtem kroků omezeným pol(n), BUNO (lze převést do)
        - M má jediný přijímací stav 
        - z přijímacícho stavu neexistuje přechod dál 
        - počáteční konfigurace: hlava vlevo 
        - během konfigurace hlava nejde doprava 
        - přijímací konfigurace: páska prázdná, hlava nejvíc vlevo 

    - transforujme M do instance kachlíkování: existuje právě když M(x) přijme 
        - barvy mezi řádky odpovídají legálním konfiguracím 
        - řada kachlíků representuje validní přechod 
        - horní a dolní okraje kódují startovací (závislá na x, jinak unikátní) a přijímajcí konfiguraci 

    - kachlíky:
        - barva (q, a): kóduje políčko na pásce s hlavou, pouze 1 na řádce  
        - ostatní políčka barva odpovídající symbolu na daném místě 
        - velikost strany čtvercové sítě f(n)
        - boční strany: prázdný symbol 
        
        - kopirovani buněk nad kterými není hlava (prázdné strany, `a` nahoře/dole)
        - přechodová funkce (BUNO L, analogicky R, N): dva kachlíky, na stranách 
            - vpravo: starý stav nahoře, nový znak dole
            - vlevo: starý znak nahoře/dole, nový stav dole) nový stav
            - musí být vedle sebe -> koduji posun/přechod
        - kopirovani příjímajícího stavu bez posunu 

    - x z A <> KACHLIK ma pripustne vykachlikovani 
        -> x z A, tj. existuje přijímající výpočet, po typech kachlíků dokážeme že lze vytvořit okachlíkování
            - délka v pohodě 
            - indukce: podle typů kachlíků
        <- máme přípustné okachlíkování, dokážeme že odpovídá konfiguraci a validním přechodům
            - validní konfigurace: právě jedna značka hlavy 
            - validní přechod: zachová právě jednu hlavu, provede update stavu/znaku 
            - skrze indukci dokážu že je zachováno a platí pro všechnz řádky
            -> dostanu validní posloupnost konfigurací 
        
- SAT v KNF je NP úplný 
    - odsimulujeme kachlíkování v SATu 
    - s^2 * |K| proměnných, každá pro lokace::typ kachlíku 

    - klazule: 
        - každé pole má kachlík 
        - každé pole má maximálně 1 kachlík 
        - pole jsou vertikálně, horizontálně kompatibilní 
        - hrany sedí 

## 3-SAT: převod SAT->3SAT
-  (e1 v e2 v e3 v e4 v ... v ek) -> (e1 v e2 v j1) and (!j1 v e3 v e4 ...) a rekurzivně rozsekat 

## Vrcholové pokrytí: 3SAT -> VP 
- nahoře spojené dva vrcholy pro každou proměnnou (x, !x), dole trojúhelník pro každou klazuli, propojení podle toho, co klazule obsahuje

## Hammiltonovka kružnice: VP -> hammilton 
- HC: 
    - vyberu vrchol v0 z původního grafu
    - z jedné strany k němu připojím hranu s novým vrcholem (začátek cesty)
    - vytvořím ještě jeden nový vrchol u, připojím na všechny sousedy v0, a pak ještě jednu novou hranu s novým vrcholem (konec cesty)
    -> procházející cesta začíná skrze v0, končí skrze souseda v0 -> odpovídá HK 

# Pseudopolynomiální algoritmy, silná NP-úplnost
- číselný problém
    - nechť A je libovolný rozhodovací problém, I instance problému; len(I): délka kódování, max(I): hodnota největšího číselného parametru 
    - A je číselný problém, pro V pol p: existuje I, že max(I) > p(len(I))
    ~ intuitivně: v problému je číselný parametr, u kterého jeho velikost ovlivní rychlost alg. a je víc než poly velikosti vstupu 
        - např.: batoh, loupežníci; ne kachlíkování 

- Pseudopolynomiální algoritmus: časová složitost je omezená pol(len(I), max(I)) ~ byl by polynomiální kdyby kodovano unárně 
    - je-li problém řešitelný Pseudopolynomiálně a nejde o číselný problém -> je ve skutečnosti polynomiální 

- Batoh: množina předmětů, ci, wi; max cena pod určitou váhu 
    - dynamické programování: dvě tabulky
        - T:NxV: podmnožina indexů prvků celkové ceny přesně c s minimalni velikostí
        - S:NxV: celková velikost prvků přesně cenz c 
    - algoritmus: 
        - for j: 0..N:          # Pro každý možný poslední prvek (ať už použitý nebo ne)
            - for c in 1..V:    # Pro každou možnou cenu 
                - T[j, c] = T[j-1,c] # Určitě alespoň stejně dobrá možnost, jako s max. posledním minulým prkvem 
                - ... 
                - pomocí prvků j lze poskládat lehčí váhu než T[j, c] -> update 
        - max c z S[n, c] < B 

        - O(n*V)

- silná NP úplnost: 
    - nechť A je NP úplný problém, A(p) je restrikce A na instance I pro něž platí, max(I) <= p(len(I))
    - A je silně NP úplný existuje poly p, že A(p) je NP-úplný problém 
    ~ A(p) nám dá jen "nečíselné problémy" instance, pokud i tihle jsou NP-úplné -> silně NP-úplný problém 

- slabá NP úplnost: pokud existuje pseudopolynomiální algoritmus 
    - jsou problémy co nejsou ani jedno 

- důsledky
    - každý nečíselný NP-úplný problém je silně NP-úplný 
    - pokud by existoval silně NP-úplný problém řešitelný pseudopoly alg. -> P = NP 
    - silně NP úplný je NP úplný i při unárním kódování 
    - i číselné NP-úplné mohou být NP-úplné: TSP 

- TSP je silně NP-úplný 
    - určitě v NP 
    - těžkost TSP(p) ukážeme na převodu hammiltonovské kružnice na poly omeyenou verzi TSP 

    - úplný graf, vzdálenost 0 pokud v původním grafu, 1 jinak 
        - existuje Hammilton právě pokud obchodní cestující najde cestu nulové délky
        - hodnoty max 1, tj. určitě z TSP(1)

## Aproximační algoritmy a schémata
- optimalizační úloha: 
    - Instance, přípustná řešení, f: přípustné řešení->hodnota 
    - úloha: přípustné řešení s nejmenší/největší hodnotou pro danou instanci 

- aproximační algoritmus s poměrem e >= 1: 
    - pro optimalizační úlohu A nalezne pro každou instanci I přípustné řešení R(I), že opt(I) <= e*R(I)

- Bin-packing: rozdělení předmětů do co nejměnšího počtu binů velikosti 1 
    - FirstFit: 
        - vždy do prvního volného koše, když není, založ nový 
        - 2-optimalizační alg: nemohou existovat 2 biny zaplněné méně než z poloviny 
        - pro libovolné k existuje I, že FF vytvoří alespoň 5/3opt košů 
            - 18k instancí: 6: 1/7+e, 6: 1/3+e, 6: 1/2+e 
            - opt: 6k košů, FF: k-košů (6x první), 3k-košů (2x prostřední), 6k košů (1x poslední)
    - FFD: First fit decreasing: setřiď podle velikosti, od nejmenších FF 
        - 11/9 OPT + 4

- aproximační schéma: 
    - vstup: racionální číslo e, vydá řešení s aproximačním poměrem 1+e 
    - PAS: polynomiální aproximační schéma: pro každé e je časová složitost algoritmu polynomiální v len(I)
    - UPAS: úplně polynomiální aproximační schéma: ALG je časově polynomiální v len(I) a 1/e 
        - UPAS nesmí mít 1/e v exponentu čas. složitosti 

- UPAS pro batoh 
    - využijeme pseudopolynomiální alg. výše, který pracuje v čase O(nV)
    - skrze zaokrouhlení a rescaling: zmenšit cenu nových předmětů 

    - c[i] = floor(c[i]/2^floor( log2(e * c[max] / n)-1 ))
    - funguje v čase: O(n^3*1/e) neb C <= 4n^2/e

- lze otočit: UPAS -> psuedopolynomiální algoritmus 
    - nechť A je opt. úloha, kde přípustná řešení celočíselnou hodnotu a existuje polynom p: V I of A: opt(I) < q(len(I), max(I))
    - pokud existuje UPAS -> existuje pseudopolynomiální algoritmus 

    - ALG je UPAS pro A 
    - pro I položíme e = 1/q(len(I), max(I)), pustíme ALG_e(I)
        - runtime je pol(len(I), max(I)) z definice UPAS 
        - opt(I) <= (1 + 1/q(len(I), max(I))) * ALG_e(I)
        -> opt(I) - ALG_e(I) < e*ALG_e(I) <= e*OPT(i) <opt(I) < q(len(I), max(I))= 1
        -> pokud jsou řešení celočíselná a rozdíl menší než 1 -> musí se rovnat 

    - nechť A je silně NP-úplná optimalizační úloha, která splňuje předpoklady UPAS->pseudopoly, pak pokud P != NP neexistuje UPAS pro A. 

## Neaproximovatelnost 
- TSP není aproximovatelný ani s konstnatním poměrem 
- předpokládejme, že existuje aproximační alg pro e: 
    - nechť G je libovolný graf kterému chceme najít Hammilton kružnici, doplníme na úplný, ohodnotíme: 1::pokud z E, e*|V| else 
    - ptáme se jestli existuje TSP <= |V|, pokud ano <> v původním grafu existuje hammlitonovska kružnice 
        - cena ne-E hran nutí libovolně aprox. algoritmus, aby ji nevybral pokud hammilt. existuje (byť jedna hrana překročí aprox. poměr)
    -> pokud by bylo polynomiální -> P = NP 

- pro TSP s trojúhelníkovou nerovností existuje 3/2 aproximační algoritmus 
- pro TSP v euklidovské rovidě existuje PAS 

## Aproximace různé úrovně NP-úplných problémů 
- rychle a libovolně dobře aproximovatelné (UPAS): batoh 
- mají alespoň PAS 
- aproximační alg. s kosntantím poměrem, ale ne libovolným (tj. ne PAS): vrcholové pokrytí 
- nemají aprox. alg: klika, obchodní cestující 
