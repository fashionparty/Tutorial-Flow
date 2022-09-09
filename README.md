## Zależności

`implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:$.$.$")`

## Flow

Mechanizm Kotlina służący do programowania reaktywnego. Jest to korutyna, która może zwracać (emitować) jedną lub więcej wartości. Pozwala na tworzenie reagującego na zmiany przepływu danych i modyfikowanie tych danych "w locie". 

```
val countdownFlow = flow {
  var x = 10
  while(x>10) {
    emit(x)
    delay(1000L)
    x--
    }
}
```

```
viewModelScope.launch {
  countDownFlow.collect {
    updateView(it)
  }
}
```

`flow.collect` - zbiera wszstko co flow wyemituje 

`flow.collectLatest` - jeśli wykonanie danego bloku trwa dłużej niż pojedyncza emisja to zostanie zebrana tylko ostatnia emisja ( nowa emisja anuluje poprzednią )

## Flow Operator

Operatory pozwalają na transformację danych pochodzacych z emisji. Operatory mozna łączyć w łańcuchy.

```
flow.filter {
  it % 2 == 0
}.collect
```

```
flow.map {
  it * it
}
```

```
flow.onEach {
  println(it)
}
```

## Terminal Flow Operators

Operatory, które robią coś z całym wynikiem flow ( wszystkimi emisjami )

```
val emissionCount = flow.count {
                      it % 2 == 0
                    }
```

```
val sum = flow.reduce { accumulator, value ->
                accumulator+value
              }
```

```
val result = flow.fold(initial = 10) { accumulator, value -> 
                  accumulator + value
                }
```
      
## Flattening Flow Operators

```
// zewnętrzny flow przekazuje emisję do wewnętrznego, następnie wynik może zostać zebrany
val result = flow.flatMapContact { value ->
                  flow {
                    emit(function(value))
                  }.collect { nextFunction(it)
                  
                  
// flatMapLatest - wewnętrzny flow pobiera tylko najnowsze emisje
// flatMapMerge - tworzy iloczyn pomiędzy flow'ami - każda emisja z zewnętrznego flow zostanie przetworzona przez każdą emisję z wewnętrznego
```

## Forwarding Flow Operators

`Buffer()` - umieszczony przed `collect` sprawi, że każda emisja zostanie obsłużona równolegle, tj. odpalona w osobnej korutynie.

`Conflate() - kiedy najstarsza emisja zostanie wykonana to zostanie zebrana jeszcze tylko aktualnie wykonywana emisja, pozostałe zostaną przerwane. Tak jak w przypadku `Buffer` przetwarza emisje równlegle.
                 
