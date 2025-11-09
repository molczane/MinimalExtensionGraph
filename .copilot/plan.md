# Plan dokumentacji algorytmu minimalnego rozszerzenia grafu G zawierającego m kopii grafu P

## 1. Wprowadzenie i kontekst problemu

### 1.1 Opis problemu
- Definicja problemu: znalezienie minimalnego rozszerzenia multigrafu G do postaci G', która zawiera co najmniej m podgrafów izomorficznych z multigrafem P
- Motywacja badań i zastosowania praktyczne
- Powiązanie z problemem izomorfizmu podgrafów
- Uzasadnienie wyboru multigrafów skierowanych jako dziedziny problemu

### 1.2 Założenia
- Grafy są reprezentowane jako macierze sąsiedztwa
- Multigrafami skierowanymi (między dowolnymi dwoma wierzchołkami może istnieć wiele krawędzi skierowanych)
- Wierzchołki są ponumerowane od 0 do n-1
- Krawędzie mogą mieć różne krotności (wielokrotne krawędzie między tą samą parą wierzchołków)

## 2. Pojęcia podstawowe i definicje formalne

### 2.1 Definicje już opracowane (referencje do sekcji dokumentacji)
- Multigraf (Definicja 2 w main.tex)
- Multigraf skierowany (Definicja 3 w main.tex)
- Izomorfizm multigrafów (Definicja 4 w main.tex)
- Podgraf multigrafu (Definicja 5 w main.tex)
- Macierz sąsiedztwa multigrafu (Definicja 7 w main.tex)
- Rozmiar multigrafu (Definicja 8 w main.tex, sekcja 3)
- Metryka w zbiorze multigrafów (sekcja 4)

### 2.2 Nowe definicje wymagające opracowania

#### 2.2.1 Rozszerzenie multigrafu
**Definicja (autorska):**
Niech G = (V_G, E_G) i G' = (V_G', E_G') będą multigrafami. Mówimy, że G' jest **rozszerzeniem** G, jeśli:
1. V_G ⊆ V_G' (zbiór wierzchołków G jest podzbiorem wierzchołków G')
2. E_G ⊆ E_G' (zbiór krawędzi G jest podzbiorem krawędzi G')

**Uzasadnienie:**
- Definicja naturalna, oparta na relacji inkluzji zbiorów
- Zgodna z intuicją: rozszerzenie grafu polega na dodaniu nowych wierzchołków i/lub krawędzi
- Zachowuje strukturę oryginalnego grafu
- Spójna z definicją podgrafu (G jest podgrafem G')

#### 2.2.2 Koszt rozszerzenia
**Definicja (autorska):**
Niech G = (V_G, E_G) i G' = (V_G', E_G') będą multigrafami takimi, że G' jest rozszerzeniem G. **Kosztem rozszerzenia** γ(G, G') nazywamy parę liczb naturalnych:
```
γ(G, G') = (|V_G' \ V_G|, |E_G' \ E_G|)
```
gdzie:
- |V_G' \ V_G| to liczba dodanych wierzchołków
- |E_G' \ E_G| to liczba dodanych krawędzi

**Uzasadnienie:**
- Koszt uwzględnia dwie podstawowe operacje rozszerzania grafu
- W kontekście algorytmu skupiamy się głównie na dodawaniu krawędzi (liczba wierzchołków jest stała)
- Możliwość definiowania różnych porządków na kosztach (leksykograficzny, suma ważona)

#### 2.2.3 Minimalne rozszerzenie zawierające m kopii podgrafu P
**Definicja (autorska, kluczowa dla projektu):**
Niech G = (V_G, E_G) i P = (V_P, E_P) będą multigrafami, gdzie |V_P| ≤ |V_G|, oraz niech m ≥ 1 będzie liczbą naturalną. **Minimalnym rozszerzeniem** G zawierającym m kopii P nazywamy multigraf G' = (V_G', E_G') spełniający następujące warunki:

1. G' jest rozszerzeniem G (V_G ⊆ V_G', E_G ⊆ E_G')
2. G' zawiera co najmniej m podgrafów parami rozłącznych wierzchołkowo, z których każdy jest izomorficzny z P
3. Koszt rozszerzenia γ(G, G') jest minimalny w sensie leksykograficznym wśród wszystkich rozszerzeń spełniających warunki 1 i 2

**Warunek minimalności (porządek leksykograficzny na kosztach):**
Dla dwóch kosztów (v₁, e₁) i (v₂, e₂):
```
(v₁, e₁) < (v₂, e₂) ⟺ v₁ < v₂ ∨ (v₁ = v₂ ∧ e₁ < e₂)
```

**Uzasadnienie:**
- Wymóg rozłączności wierzchołkowej m kopii zapewnia, że są to rzeczywiście odrębne podgrafy
- Porządek leksykograficzny priorytetyzuje minimalizację liczby dodanych wierzchołków, a następnie krawędzi
- W implementacji zakładamy V_G = V_G' (nie dodajemy wierzchołków), więc minimalizujemy tylko liczbę dodanych krawędzi
- Definicja jest operacyjna i pozwala na konstrukcję algorytmów

#### 2.2.4 Brakujące krawędzie dla embedding'u
**Definicja (autorska, pomocnicza):**
Niech G = (V_G, E_G) i P = (V_P, E_P) będą multigrafami, gdzie |V_P| = k ≤ |V_G| = n. Niech f: V_P → V_G będzie injekcją (potencjalnym osadzeniem P w G). **Zbiorem brakujących krawędzi** dla osadzenia f nazywamy multizbiór:
```
Δ(f, G, P) = {(f(u), f(v)) : liczba krawędzi (u,v) w P > liczba krawędzi (f(u), f(v)) w G}
```
z krotnościami:
```
mult_Δ(f(u), f(v)) = max(0, A_P[u][v] - A_G[f(u)][f(v)])
```
gdzie A_P, A_G są macierzami sąsiedztwa.

**Uzasadnienie:**
- Umożliwia kwantyfikację "odległości" między potencjalnym osadzeniem a rzeczywistym izomorfizmem
- Stanowi podstawę algorytmu konstrukcji minimalnego rozszerzenia
- Uwzględnia krotności krawędzi (multigrafowość)

#### 2.2.5 Osadzenie k-wierzchołkowe i permutacja
**Definicja (autorska, operacyjna):**
Osadzenie k-wierzchołkowe grafu P w grafie G definiujemy przez:
1. **k-kombinację** - podzbiór C ⊆ V_G taki, że |C| = k = |V_P|
2. **k-permutację** - bijekcję π: V_P → C

Para (C, π) definiuje potencjalne osadzenie P w G poprzez podgraf indukowany przez C z odpowiednim mapowaniem wierzchołków.

**Uzasadnienie:**
- Formalizuje procedurę przeszukiwania przestrzeni możliwych osadzeń
- Bezpośrednio odpowiada implementacji (kombinacje i permutacje w kodzie)
- Liczba możliwych osadzeń: C(n, k) × k!

## 3. Algorytm dokładny dla m kopii grafu P

### 3.1 Przegląd algorytmu
- Algorytm oparty na pełnym przeszukiwaniu przestrzeni rozwiązań
- Strategia: wygeneruj wszystkie możliwe m-tuple osadzeń, dla każdego oblicz wymagany zbiór krawędzi
- Wybierz osadzenie minimalizujące liczbę dodanych krawędzi

### 3.2 Struktura algorytmu

#### 3.2.1 Faza 1: Generowanie osadzeń i macierzy brakujących krawędzi

**Krok 1: Generowanie k-kombinacji wierzchołków G**
```
Input: G = (V_G, E_G), |V_G| = n, k = |V_P|
Output: Zbiór wszystkich k-kombinacji C = {C₁, C₂, ..., C_N}, gdzie N = C(n,k)

Dla każdej k-kombinacji C_j ⊆ V_G:
    indexToSubset[j] = C_j
```
- Implementacja: `combinationsK()` z `Combinations.kt`
- Złożoność: O(C(n,k)) kombinacji do wygenerowania
- Liczba kombinacji: C(n, k) = n!/(k!(n-k)!)

**Krok 2: Generowanie permutacji wierzchołków P**
```
Input: P = (V_P, E_P), |V_P| = k
Output: Zbiór wszystkich permutacji π = {π₁, π₂, ..., π_M}, gdzie M = k!

Dla każdej permutacji π_i: V_P → V_P:
    indexToPermutation[i] = π_i
```
- Implementacja: `permutations()` z `Permutations.kt`
- Złożoność: O(k! × k) - generowanie k! permutacji, każda długości k
- Liczba permutacji: k!

**Krok 3: Obliczanie brakujących krawędzi dla każdej pary (kombinacja, permutacja)**
```
Input: G, P, indexToSubset, indexToPermutation
Output: missingEdgesMatrix[i][j] = lista brakujących krawędzi dla permutacji i i kombinacji j

Dla i = 0 do |indexToPermutation| - 1:
    Dla j = 0 do |indexToSubset| - 1:
        π = indexToPermutation[i]  // permutacja P
        C = indexToSubset[j]        // kombinacja wierzchołków G
        
        // Utwórz osadzenie f: V_P → V_G przez π i C
        // f(v) = C[π(v)] dla v ∈ V_P
        
        missingEdgesMatrix[i][j] = []
        
        Dla u = 0 do k-1:
            Dla v = 0 do k-1:
                u_mapped = C[π(u)]  // wierzchołek w G odpowiadający u w P
                v_mapped = C[π(v)]  // wierzchołek w G odpowiadający v w P
                
                // Liczba krawędzi w permutowanym P
                edges_in_P = A_P[π(u)][π(v)]
                // Liczba krawędzi w przyciętym G
                edges_in_G = A_G[u_mapped][v_mapped]
                
                missing = max(0, edges_in_P - edges_in_G)
                
                Dla t = 0 do missing - 1:
                    missingEdgesMatrix[i][j].add((u_mapped, v_mapped))
```
- Złożoność pojedynczego obliczenia: O(k²)
- Całkowita złożoność tego kroku: O(C(n,k) × k! × k²)
- Pamięć: O(C(n,k) × k! × k²) w najgorszym przypadku

**Uzasadnienie poprawności Kroku 3:**
- Permutacja π reprezentuje potencjalne mapowanie wierzchołków P na wierzchołki kombinacji C
- Dla każdej pary wierzchołków (u,v) w P sprawdzamy, czy odpowiadająca para w G ma wystarczającą liczbę krawędzi
- Jeśli nie, dodajemy brakujące krawędzie do listy

#### 3.2.2 Faza 2: Znajdowanie minimalnego rozszerzenia dla m kopii

**Krok 4: Generowanie m-kombinacji osadzeń (kombinacje k-podzbiorów)**
```
Input: indexToSubset (N elementów), m
Output: indexToMSizedCombination - wszystkie m-kombinacje indeksów podzbiorów

Dla każdej m-kombinacji indeksów {j₁, j₂, ..., j_m} z {0, 1, ..., N-1}:
    indexToMSizedCombination[id] = {j₁, j₂, ..., j_m}
```
- Implementacja: `combinationsK(indexToSubset.keys, m)`
- Liczba m-kombinacji: C(C(n,k), m)
- Złożoność: O(C(C(n,k), m))

**Krok 5: Generowanie m-krotek permutacji (produkty kartezjańskie)**
```
Input: indexToPermutation (M = k! elementów), m
Output: Dla każdej m-kombinacji podzbiorów, wszystkie m-krotki permutacji

Dla każdej m-krotki indeksów permutacji (i₁, i₂, ..., i_m) gdzie każde i_t ∈ {0, ..., M-1}:
    // Reprezentuje wybór m osadzeń: jedno dla każdej kopii P
```
- Implementacja: `productSequences(indexToPermutation.keys, m)`
- Liczba m-krotek: (k!)^m
- Złożoność: O((k!)^m)

**Krok 6: Obliczanie unii zbiorów krawędzi z obsługą krotności**
```
Input: missingEdgesMatrix, indexToMSizedCombination, m-krotki permutacji
Output: minimalListOfAddedEdges - minimalna lista krawędzi do dodania

minimalListOfAddedEdges = null
minimalSize = ∞

Dla każdej m-kombinacji podzbiorów {j₁, ..., j_m}:
    Dla każdej m-krotki permutacji (i₁, ..., i_m):
        edgeFrequencyMap = {}  // mapa: krawędź → maksymalna krotność
        
        // Dla każdej z m kopii P:
        Dla t = 0 do m-1:
            subsetIndex = j_t
            permutationIndex = i_t
            missingEdges = missingEdgesMatrix[permutationIndex][subsetIndex]
            
            // Zlicz krotności krawędzi dla tej kopii
            localFrequency = {}
            Dla każdej krawędzi e w missingEdges:
                localFrequency[e]++
            
            // Aktualizuj globalną mapę: bierzemy maksimum
            Dla każdej krawędzi e w localFrequency:
                edgeFrequencyMap[e] = max(edgeFrequencyMap[e], localFrequency[e])
        
        // Konwertuj mapę częstości na listę krawędzi
        addedEdgesList = []
        Dla każdej (krawędź e, krotność freq) w edgeFrequencyMap:
            Dla t = 0 do freq - 1:
                addedEdgesList.add(e)
        
        // Aktualizuj minimum
        Jeśli |addedEdgesList| < minimalSize:
            minimalSize = |addedEdgesList|
            minimalListOfAddedEdges = addedEdgesList
            
Return minimalListOfAddedEdges
```

**Uzasadnienie operacji maksimum na krotnościach:**
- Jeśli m kopii P wymaga tej samej krawędzi, wystarczy dodać ją jeden raz (kopie mogą "dzielić" krawędzie)
- Jeśli kopia 1 wymaga krawędzi (u,v) z krotnością 2, a kopia 2 wymaga (u,v) z krotnością 3, musimy dodać 3 kopie krawędzi (maksimum)
- To zapewnia, że każda kopia P będzie miała wystarczającą liczbę krawędzi
- Minimalizuje liczbę dodanych krawędzi (nie sumujemy, tylko bierzemy maksimum)

**Dowód poprawności:**
1. **Kompletność**: Algorytm rozważa wszystkie możliwe m-tuple osadzeń m kopii P w G
2. **Poprawność**: Dla każdej m-krotki osadzeń obliczamy dokładnie tyle krawędzi, ile jest potrzebnych (poprzez maksimum krotności)
3. **Minimalność**: Przeszukujemy całą przestrzeń rozwiązań i wybieramy osadzenie z najmniejszą liczbą krawędzi

### 3.3 Analiza złożoności obliczeniowej

#### 3.3.1 Złożoność czasowa

**Notacja:**
- n = |V_G| - liczba wierzchołków dużego grafu G
- k = |V_P| - liczba wierzchołków małego grafu P
- m - liczba wymaganych kopii P w G

**Rozbicie na fazy:**

1. **Faza 1a - Generowanie kombinacji:** O(C(n,k) × k)
   - Liczba kombinacji: C(n,k)
   - Koszt generowania jednej kombinacji: O(k)

2. **Faza 1b - Generowanie permutacji:** O(k! × k)
   - Liczba permutacji: k!
   - Koszt generowania jednej permutacji: O(k)

3. **Faza 1c - Obliczanie missingEdgesMatrix:** O(C(n,k) × k! × k²)
   - Dla każdej z C(n,k) kombinacji
   - Dla każdej z k! permutacji
   - Porównanie k² par krawędzi

4. **Faza 2a - Generowanie m-kombinacji osadzeń:** O(C(C(n,k), m) × m)
   - Liczba m-kombinacji: C(C(n,k), m)

5. **Faza 2b - Główna pętla przeszukiwania:**
   ```
   O(C(C(n,k), m) × (k!)^m × m × k²)
   ```
   - Dla każdej m-kombinacji podzbiorów: C(C(n,k), m)
   - Dla każdej m-krotki permutacji: (k!)^m
   - Dla każdej z m kopii: m
   - Obliczanie częstości krawędzi: O(k²) dla jednej kopii

**Całkowita złożoność czasowa:**
```
T(n, k, m) = O(C(n,k) × k! × k²) + O(C(C(n,k), m) × (k!)^m × m × k²)
```

**Dominujący składnik** dla małych m:
```
T(n, k, m) = O(C(n,k) × k! × k²)
```

**Dominujący składnik** dla większych m:
```
T(n, k, m) = O(C(C(n,k), m) × (k!)^m × m × k²)
```

**Oszacowania asymptotyczne:**
- C(n,k) = O(n^k / k!) - wielomianowe względem n dla stałego k
- k! - silniowe względem k
- C(C(n,k), m) ≈ O((n^k)^m) dla dużych n
- (k!)^m - wykładnicze względem m

**Złożoność w praktyce:**
- Dla k = 3, n = 10, m = 2:
  - C(10,3) = 120
  - 3! = 6
  - C(120, 2) = 7140
  - (3!)^2 = 36
  - Operacje: ≈ 120 × 6 × 9 + 7140 × 36 × 2 × 9 ≈ 6480 + 4,630,080 ≈ 4,6M

#### 3.3.2 Złożoność pamięciowa

**Główne struktury danych:**

1. **missingEdgesMatrix:** O(C(n,k) × k! × k²)
   - Tablica 2D o wymiarach k! × C(n,k)
   - Każda komórka zawiera listę brakujących krawędzi (w najgorszym O(k²) krawędzi)

2. **indexToSubset:** O(C(n,k) × k)
   - Przechowuje C(n,k) kombinacji, każda długości k

3. **indexToPermutation:** O(k! × k)
   - Przechowuje k! permutacji, każda długości k

4. **Zmienne tymczasowe w głównej pętli:** O(k²)

**Całkowita złożoność pamięciowa:**
```
S(n, k, m) = O(C(n,k) × k! × k²)
```

Uwaga: Złożoność pamięciowa jest niezależna od m (nie przechowujemy wszystkich m-krotek).

#### 3.3.3 Charakterystyka algorytmu

**Klasa złożoności:**
- Problem jest **NP-trudny** (redukcja z problemu izomorfizmu podgrafów)
- Algorytm dokładny ma złożoność **wykładniczą** względem k (ze względu na k!)
- Algorytm ma złożoność **wielomianowo-wykładniczą** względem m

**Praktyczne ograniczenia:**
- Algorytm jest wykonalny dla małych wartości k (k ≤ 6-7) i n ≤ 20
- Dla większych wartości k lub n algorytm staje się niepraktyczny
- Wartość m ma mniejszy wpływ na czas wykonania niż k (dla małych m)

### 3.4 Pseudokod algorytmu

```
Algorithm: MinimalGraphExtension(G, P, m)
Input: 
    G = (V_G, E_G) - multigraf "duży", |V_G| = n
    P = (V_P, E_P) - multigraf "mały", |V_P| = k
    m - liczba wymaganych kopii P
Output:
    Lista krawędzi do dodania do G, aby zawierał m kopii P
    
1. // Faza 1: Generowanie osadzeń i macierzy brakujących krawędzi
2. combinations ← GenerateCombinations(V_G, k)
3. indexToSubset ← IndexCombinations(combinations)
4. permutations ← GeneratePermutations(V_P)
5. indexToPermutation ← IndexPermutations(permutations)
6. 
7. missingEdgesMatrix ← Array[|indexToPermutation|][|indexToSubset|]
8. 
9. for i ← 0 to |indexToPermutation| - 1 do
10.     for j ← 0 to |indexToSubset| - 1 do
11.         π ← indexToPermutation[i]
12.         C ← indexToSubset[j]
13.         missingEdgesMatrix[i][j] ← []
14.         
15.         for u ← 0 to k-1 do
16.             for v ← 0 to k-1 do
17.                 u_mapped ← C[π[u]]
18.                 v_mapped ← C[π[v]]
19.                 edges_in_P ← A_P[π[u]][π[v]]
20.                 edges_in_G ← A_G[u_mapped][v_mapped]
21.                 missing ← max(0, edges_in_P - edges_in_G)
22.                 
23.                 for t ← 0 to missing - 1 do
24.                     missingEdgesMatrix[i][j].append((u_mapped, v_mapped))
25. 
26. // Faza 2: Znajdowanie minimalnego rozszerzenia dla m kopii
27. mSizedCombinations ← GenerateCombinations(indexToSubset.keys, m)
28. indexToMSizedCombination ← IndexCombinations(mSizedCombinations)
29. 
30. minimalListOfAddedEdges ← null
31. minimalSize ← ∞
32. 
33. for each mComb in indexToMSizedCombination do
34.     subsetsNumbers ← indexToMSizedCombination[mComb]
35.     
36.     for each mTuple in ProductSequences(indexToPermutation.keys, m) do
37.         permutationsNumbers ← mTuple
38.         edgeFrequencyMap ← EmptyMap()
39.         
40.         for t ← 0 to m-1 do
41.             subsetIndex ← subsetsNumbers[t]
42.             permutationIndex ← permutationsNumbers[t]
43.             missingEdges ← missingEdgesMatrix[permutationIndex][subsetIndex]
44.             
45.             localFrequency ← EmptyMap()
46.             for each edge in missingEdges do
47.                 localFrequency[edge] ← localFrequency[edge] + 1
48.             
49.             for each (edge, freq) in localFrequency do
50.                 edgeFrequencyMap[edge] ← max(edgeFrequencyMap[edge], freq)
51.         
52.         addedEdgesList ← []
53.         for each (edge, freq) in edgeFrequencyMap do
54.             for t ← 0 to freq - 1 do
55.                 addedEdgesList.append(edge)
56.         
57.         if |addedEdgesList| < minimalSize then
58.             minimalSize ← |addedEdgesList|
59.             minimalListOfAddedEdges ← addedEdgesList
60. 
61. return minimalListOfAddedEdges
```

### 3.5 Dowód poprawności algorytmu

#### Twierdzenie (Poprawność algorytmu)
Algorytm MinimalGraphExtension zwraca minimalną listę krawędzi do dodania do G, aby zawierał m rozłącznych wierzchołkowo kopii P.

**Dowód:**

**Część 1: Kompletność (algorytm znajduje rozwiązanie, jeśli istnieje)**

Załóżmy, że istnieje rozszerzenie G' grafu G zawierające m rozłącznych wierzchołkowo kopii P, osiągnięte przez dodanie zbioru krawędzi E_add.

1. Dla każdej z m kopii P w G' istnieje:
   - k-kombinacja wierzchołków C_t ⊆ V_G (t = 1, ..., m)
   - Permutacja π_t: V_P → V_P
   - Takie że podgraf G' indukowany przez C_t z odpowiednim mapowaniem jest izomorficzny z P

2. Kombinacje C_t są rozłączne (C_i ∩ C_j = ∅ dla i ≠ j), bo kopie są rozłączne wierzchołkowo

3. Algorytm generuje wszystkie możliwe m-kombinacje k-podzbiorów w linii 27

4. Dla każdej m-kombinacji, algorytm generuje wszystkie możliwe m-krotki permutacji w linii 36

5. Zatem algorytm rozważy kombinację {C₁, ..., C_m} i krotkę permutacji (π₁, ..., π_m) odpowiadającą rzeczywistemu rozwiązaniu

6. Dla tej kombinacji i krotki permutacji, algorytm obliczy dokładnie te same krawędzie, które są w E_add (linie 40-50)

**Część 2: Poprawność (dodane krawędzie są wystarczające)**

Dla dowolnej m-kombinacji podzbiorów i m-krotki permutacji rozważanej przez algorytm:

1. Dla każdej z m kopii (linie 40-50):
   - Algorytm oblicza brakujące krawędzie w missingEdgesMatrix[permutationIndex][subsetIndex]
   - Te krawędzie są dokładnie tymi, których brakuje do stworzenia izomorfizmu

2. Operacja maksimum na krotnościach (linia 50) zapewnia:
   - Jeśli wielokrotne kopie potrzebują tej samej krawędzi (u,v) z krotnościami k₁, k₂, ..., dodajemy max(k₁, k₂, ...) kopii
   - To jest wystarczające, bo krawędzie mogą być współdzielone między kopiami
   - To jest konieczne, bo każda kopia wymaga odpowiedniej krotności

3. Po dodaniu krawędzi z addedEdgesList do G:
   - Każda z m kopii ma wystarczającą liczbę krawędzi między każdą parą wierzchołków
   - Każda kopia jest izomorficzna z P

**Część 3: Minimalność (algorytm znajduje minimum)**

1. Algorytm przeszukuje wszystkie możliwe sposoby osadzenia m kopii P w G (linie 33-36)

2. Dla każdego sposobu oblicza minimalną liczbę krawędzi potrzebnych do realizacji tego osadzenia (linie 38-55)

3. Wybiera osadzenie wymagające najmniejszej liczby krawędzi (linie 57-59)

4. Nie istnieje sposób osadzenia m kopii P w G wymagający mniej krawędzi, bo wszystkie sposoby zostały rozważone

**Wniosek:** Algorytm jest poprawny - zwraca minimalną liczbę krawędzi wystarczających do stworzenia m rozłącznych wierzchołkowo kopii P w G. ∎

### 3.6 Przykład działania algorytmu

**Dane wejściowe:**
```
G - graf z 8 wierzchołkami (sample_graphs5.txt):
Macierz sąsiedztwa G (8×8):
2 1 1 0 0 0 0 1
1 2 1 0 0 0 0 0
1 1 2 1 0 0 0 0
0 0 1 0 1 0 0 0
0 0 0 1 0 1 0 0
0 0 0 0 1 0 1 0
0 0 0 0 0 1 0 1
1 0 0 0 0 0 1 0

P - graf z 3 wierzchołkami:
Macierz sąsiedztwa P (3×3):
2 1 1
1 2 1
1 1 2

m = 2 (szukamy 2 kopii P)
```

**Krok 1: Generowanie kombinacji**
- n = 8, k = 3
- Liczba kombinacji: C(8,3) = 56
- Przykładowe kombinacje:
  - C₀ = {0, 1, 2}
  - C₁ = {0, 1, 3}
  - C₂ = {0, 1, 4}
  - ...
  - C₅₅ = {5, 6, 7}

**Krok 2: Generowanie permutacji**
- k = 3
- Liczba permutacji: 3! = 6
- Wszystkie permutacje:
  - π₀ = [0, 1, 2]
  - π₁ = [0, 2, 1]
  - π₂ = [1, 0, 2]
  - π₃ = [1, 2, 0]
  - π₄ = [2, 0, 1]
  - π₅ = [2, 1, 0]

**Krok 3: Obliczanie brakujących krawędzi**

Dla kombinacji C₀ = {0, 1, 2} i permutacji π₀ = [0, 1, 2]:
- Podgraf G indukowany przez {0, 1, 2}:
  ```
  2 1 1
  1 2 1
  1 1 2
  ```
- P z permutacją π₀ (bez zmian):
  ```
  2 1 1
  1 2 1
  1 1 2
  ```
- Brakujące krawędzie: [] (puste - grafy są identyczne!)
- missingEdgesMatrix[0][0] = []

Dla kombinacji C₁ = {0, 1, 3} i permutacji π₀ = [0, 1, 2]:
- Podgraf G indukowany przez {0, 1, 3}:
  ```
  2 1 0
  1 2 0
  0 0 0
  ```
- P z permutacją π₀:
  ```
  2 1 1
  1 2 1
  1 1 2
  ```
- Brakujące krawędzie:
  - (0,3): brakuje 1 krawędź
  - (1,3): brakuje 1 krawędź
  - (3,0): brakuje 1 krawędź
  - (3,1): brakuje 1 krawędź
  - (3,3): brakują 2 pętle
- missingEdgesMatrix[0][1] = [(0,3), (1,3), (3,0), (3,1), (3,3), (3,3)]

**Krok 4: Znajdowanie m = 2 kopii**

Generowanie 2-kombinacji z 56 podzbiorów: C(56, 2) = 1540

Przykład: rozważmy m-kombinację {C₀, C₅₅} = {{0,1,2}, {5,6,7}}
- Te kombinacje są rozłączne wierzchołkowo ✓

Dla każdej 2-krotki permutacji (np. (π₀, π₀)):
- Kopia 1: missingEdgesMatrix[0][0] = []
- Kopia 2: missingEdgesMatrix[0][55] = [...] (załóżmy, że też pusta)
- edgeFrequencyMap = {} (pusta)
- addedEdgesList = []
- Rozmiar: 0 ← **To jest optymalne rozwiązanie!**

**Wynik:**
```
Minimalna liczba krawędzi do dodania: 0
Dodane krawędzie: []
Osadzenie kopii 1: wierzchołki {0,1,2} z permutacją [0,1,2]
Osadzenie kopii 2: wierzchołki {5,6,7} z permutacją [0,1,2] (lub inna, zależnie od obliczenia)
```

**Interpretacja:**
- Graf G już zawiera co najmniej 2 kopie grafu P
- Nie trzeba dodawać żadnych krawędzi
- Jest to minimalny wynik możliwy do osiągnięcia

## 4. Optymalizacje i możliwe ulepszenia

### 4.1 Optymalizacje implementacyjne

#### 4.1.1 Wczesne przerywanie (pruning)
- Jeśli podczas budowania addedEdgesList rozmiar przekroczy dotychczasowe minimum, przerwij obliczenia dla tej konfiguracji
- Oszczędność czasu w pesymistycznych przypadkach

#### 4.1.2 Wykorzystanie symetrii grafu
- Jeśli graf P ma symetrie (automorfizmy), wiele permutacji da identyczne wyniki
- Można zredukować liczbę rozważanych permutacji poprzez grupowanie klas równoważności

#### 4.1.3 Sortowanie kombinacji według "obiecujących" cech
- Najpierw rozważaj kombinacje, które już mają wiele krawędzi odpowiadających P
- Może prowadzić do znalezienia dobrego rozwiązania wcześniej

#### 4.1.4 Przetwarzanie równoległe
- Obliczenia dla różnych m-kombinacji są niezależne
- Możliwość równoległego przetwarzania na wielu rdzeniach/maszynach

### 4.2 Heurystyki i algorytmy aproksymacyjne

#### 4.2.1 Algorytm zachłanny (Greedy)
**Idea:** 
- Zamiast rozważać wszystkie możliwe m-kombinacje osadzeń, wybieraj zachłannie następne najlepsze osadzenie
- Dla każdej kolejnej kopii P wybieraj osadzenie wymagające najmniej nowych krawędzi (biorąc pod uwagę już dodane)

**Złożoność:** O(m × C(n,k) × k! × k²)
**Jakość:** Nie gwarantuje optymalności, ale może dać dobre przybliżenie w krótszym czasie

#### 4.2.2 Algorytm genetyczny
**Idea:**
- Populacja: zbiór kandydatów rozwiązań (m-krotki osadzeń)
- Funkcja przystosowania: liczba krawędzi do dodania (minimalizowana)
- Operatory: krzyżowanie (wymiana osadzeń między rozwiązaniami), mutacja (losowa zmiana osadzenia)
- Selekcja: wybór najlepszych rozwiązań do następnego pokolenia

**Zalety:** Możliwość znalezienia dobrych rozwiązań dla dużych instancji
**Wady:** Brak gwarancji optymalności, wymaga tuningu parametrów

#### 4.2.3 Algorytm symulowanego wyżarzania (Simulated Annealing)
**Idea:**
- Start: losowa m-krotka osadzeń
- Iteracyjnie: modyfikuj losowo jedno osadzenie, akceptuj jeśli poprawia rozwiązanie lub z pewnym prawdopodobieństwem (malejącym z czasem)
- Pozwala na wyjście z lokalnych minimów

#### 4.2.4 Algorytm oparty na programowaniu całkowitoliczbowym (ILP)
**Idea:**
- Sformułuj problem jako ILP:
  - Zmienne binarne: x_ij = 1 jeśli osadzenie i jest wybrane dla kopii j
  - Zmienne: y_e = liczba kopii krawędzi e do dodania
  - Ograniczenia: każda kopia musi mieć dokładnie jedno osadzenie, krawędzi musi być wystarczająco
  - Cel: minimalizuj Σ_e y_e

**Zalety:** Optymalne rozwiązanie (jeśli solver zakończy się w rozsądnym czasie)
**Wady:** Może być wolne dla dużych instancji

### 4.3 Redukcja przestrzeni przeszukiwania

#### 4.3.1 Filtrowanie nieizomorficznych osadzeń
- Przed główną pętlą: dla każdej pary (permutacja, kombinacja) sprawdź, czy stopnie wierzchołków się zgadzają
- Jeśli nie, to osadzenie nie może prowadzić do izomorfizmu - pomiń je

#### 4.3.2 Wykorzystanie dolnych ograniczeń
- Oblicz dolne ograniczenie na liczbę potrzebnych krawędzi (np. suma brakujących krawędzi w każdej kopii / m)
- Jeśli dolne ograniczenie > dotychczasowe minimum, pomiń tę konfigurację

## 5. Struktura dokumentacji końcowej

### 5.1 Schemat raportu w LaTeX

**Sekcja 6: Minimalne rozszerzenie multigrafu zawierającego m kopii podgrafu P**

Podsekcje:
1. **Motywacja i sformułowanie problemu**
   - Opis problematyki
   - Zastosowania praktyczne
   - Powiązanie z problemem izomorfizmu podgrafów

2. **Definicje formalne**
   - Rozszerzenie multigrafu (Definicja + uzasadnienie)
   - Koszt rozszerzenia (Definicja + uzasadnienie)
   - Minimalne rozszerzenie zawierające m kopii (Definicja + uzasadnienie)
   - Brakujące krawędzie dla embedding'u (Definicja + uzasadnienie)
   - Osadzenie k-wierzchołkowe (Definicja + uzasadnienie)

3. **Algorytm dokładny**
   - Przegląd algorytmu
   - Szczegółowy opis faz i kroków
   - Pseudokod (z wykorzystaniem środowiska algorithm/algorithmic)
   - Przykład działania (z rysunkami grafów)

4. **Dowód poprawności**
   - Twierdzenie o poprawności
   - Dowód kompletności
   - Dowód poprawności
   - Dowód minimalności

5. **Analiza złożoności obliczeniowej**
   - Analiza złożoności czasowej (z wyprowadzeniem)
   - Analiza złożoności pamięciowej
   - Oszacowania dla konkretnych wartości parametrów
   - Charakterystyka klasy złożoności (NP-trudność)

6. **Optymalizacje i heurystyki**
   - Możliwe optymalizacje implementacyjne
   - Algorytmy aproksymacyjne (przegląd)
   - Metody redukcji przestrzeni przeszukiwania

7. **Eksperymenty obliczeniowe**
   - Opis zestawu testowego
   - Wyniki dla różnych parametrów (n, k, m)
   - Analiza wyników
   - Porównanie z heurystykami (jeśli zaimplementowano)

8. **Wnioski i dalsze kierunki badań**
   - Podsumowanie osiągnięć
   - Ograniczenia podejścia dokładnego
   - Propozycje dalszych prac (algorytmy aproksymacyjne, większe instancje)

### 5.2 Elementy graficzne do przygotowania

1. **Diagram ilustrujący pojęcie rozszerzenia grafu**
   - Graf G (oryginalny)
   - Graf G' (rozszerzony)
   - Zaznaczone dodane krawędzie

2. **Ilustracja osadzenia k-wierzchołkowego**
   - Graf P (mały)
   - Graf G (duży) z zaznaczoną k-kombinacją wierzchołków
   - Strzałki pokazujące mapowanie (permutację)

3. **Schemat blokowy algorytmu**
   - Wizualizacja faz algorytmu
   - Przepływ danych między fazami

4. **Przykład działania algorytmu krok po kroku**
   - Seria rysunków pokazująca:
     - Wejściowe grafy G i P
     - Przykładową kombinację wierzchołków
     - Przykładową permutację
     - Brakujące krawędzie
     - Końcowy wynik

5. **Wykresy wyników eksperymentów**
   - Czas wykonania vs. n (dla stałych k, m)
   - Czas wykonania vs. k (dla stałych n, m)
   - Czas wykonania vs. m (dla stałych n, k)
   - Liczba dodanych krawędzi w optymalnym rozwiązaniu

### 5.3 Bibliografia

**Źródła do przeszukania i zacytowania:**

1. **Podstawy teorii grafów i multigrafów:**
   - Diestel, R. (2017). *Graph Theory*. Springer.
   - West, D. B. (2001). *Introduction to Graph Theory*. Prentice Hall.

2. **Problem izomorfizmu (pod)grafów:**
   - Cook, S. A., & McKee, P. (1999). *Subgraph isomorphism in planar graphs and related problems*. Journal of the ACM.
   - Ullmann, J. R. (1976). *An algorithm for subgraph isomorphism*. Journal of the ACM.
   - Cordella, L. P., Foggia, P., Sansone, C., & Vento, M. (2004). *A (sub)graph isomorphism algorithm for matching large graphs*. IEEE Transactions on Pattern Analysis and Machine Intelligence.

3. **Rozszerzenia grafów:**
   - Artykuły o graph augmentation, graph completion
   - Literatura z zakresu graph modification problems

4. **Algorytmy kombinatoryczne:**
   - Knuth, D. E. (2011). *The Art of Computer Programming, Volume 4A: Combinatorial Algorithms, Part 1*. Addison-Wesley.
   - Ruskey, F. *Combinatorial Generation* (dostępne online)

5. **Złożoność obliczeniowa:**
   - Garey, M. R., & Johnson, D. S. (1979). *Computers and Intractability: A Guide to the Theory of NP-Completeness*. W. H. Freeman.

6. **Algorytmy aproksymacyjne i heurystyki:**
   - Vazirani, V. V. (2001). *Approximation Algorithms*. Springer.
   - Literatura o algorytmach genetycznych, simulated annealing

**Format cytowań:** 
- Używać BibTeX w pliku `refs.bib`
- Styl cytowań: IEEE lub ACM (zgodnie z wymaganiami przedmiotu)

## 6. Plan implementacji dokumentacji

### 6.1 Harmonogram prac

**Etap 1: Definicje formalne (1-2 dni)**
- Redakcja definicji w LaTeX
- Sformułowanie uzasadnień
- Przegląd literatury i dodanie cytowań

**Etap 2: Opis algorytmu (2-3 dni)**
- Szczegółowy opis faz algorytmu
- Napisanie pseudokodu w LaTeX
- Przygotowanie przykładu działania

**Etap 3: Analiza teoretyczna (2-3 dni)**
- Wyprowadzenie złożoności czasowej i pamięciowej
- Sformułowanie i udowodnienie twierdzenia o poprawności
- Analiza NP-trudności

**Etap 4: Eksperymenty (2-3 dni)**
- Przygotowanie zestawu testowego
- Uruchomienie testów dla różnych parametrów
- Analiza i wizualizacja wyników

**Etap 5: Optymalizacje (1-2 dni)**
- Opis możliwych optymalizacji
- Przegląd heurystyk (bez implementacji, opis teoretyczny)

**Etap 6: Elementy graficzne (2-3 dni)**
- Przygotowanie diagramów i ilustracji
- Stworzenie wykresów z wyników eksperymentów
- Integracja z dokumentem LaTeX

**Etap 7: Finalizacja (1-2 dni)**
- Redakcja całości
- Korekta
- Kompilacja i sprawdzenie PDF

**Łączny czas:** 11-18 dni roboczych

### 6.2 Narzędzia

- **LaTeX:** Redakcja dokumentacji
- **TikZ/PGFPlots:** Rysunki grafów i diagramów
- **Python/Matplotlib:** Generowanie wykresów z danych eksperymentalnych
- **BibTeX:** Zarządzanie bibliografią
- **Git:** Wersjonowanie dokumentacji

### 6.3 Podział zadań (w przypadku pracy zespołowej)

- **Osoba 1:** Definicje formalne, literatura
- **Osoba 2:** Opis algorytmu, pseudokod
- **Osoba 3:** Analiza złożoności, dowód poprawności
- **Osoba 4:** Eksperymenty, optymalizacje, elementy graficzne

## 7. Podsumowanie

Ten plan dokumentacji obejmuje wszystkie kluczowe aspekty algorytmu minimalnego rozszerzenia grafu G zawierającego m kopii grafu P:

1. **Definicje formalne** - precyzyjne zdefiniowanie wszystkich pojęć z uzasadnieniami
2. **Opis algorytmu** - szczegółowy opis faz, kroków i pseudokod
3. **Dowód poprawności** - formalne uzasadnienie, że algorytm działa zgodnie z oczekiwaniami
4. **Analiza złożoności** - dokładne oszacowania czasowe i pamięciowe
5. **Optymalizacje** - propozycje ulepszeń i alternatywnych podejść
6. **Eksperymenty** - weryfikacja empiryczna

Dokumentacja będzie spójna z obecną strukturą projektu (pliki Kotlin, istniejący raport LaTeX) i gotowa do włączenia do głównego dokumentu sprawozdania.

