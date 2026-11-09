Integrated Itinerary and Fare Engine - Metro del Valle de Aburra
SI1002-7 Languages and Paradigms of Computation, 2026-2, Second Practice (20%)

Integrantes:
Miguel [apellido]
[Nombre del compañero/a]

Como compilar:
g++ -std=c++17 -Wall -Wextra main.cpp -o fare_engine
./fare_engine

Compila sin errores ni warnings. Si lo corren en OnlineGDB hay que poner el
language standard en C++17 desde las opciones del compilador, si no toca
std::make_unique como si no existiera.

Pregunta 1: que pasa si el destructor de TransportMode no es virtual

Network guarda los modos en unique_ptr<TransportMode>. Cuando ese puntero se
destruye, llama al destructor a traves del tipo base, sin saber si el objeto
real es un MetroLine o un TouristCable. Si el destructor no fuera virtual esa
llamada se resuelve en tiempo de compilacion y solo corre ~TransportMode(),
nunca el destructor de la clase derivada real. Aca no se nota con un crash
porque ninguna clase hija maneja un recurso propio, pero el problema es real:
cualquier cosa que una clase derivada llegara a necesitar liberar (memoria,
un archivo) se quedaria sin liberar. Es comportamiento indefinido borrar
polimorficamente con un destructor base no virtual, por eso no es opcional.

Pregunta 2: por que una jerarquia de clases abstractas en vez de un enum con switch

Con un enum y switch, cada modo nuevo obliga a tocar un switch distinto por
cada cosa que varia (tarifa, si abre ventana, el texto de describe, etc), y
es facil que se olvide actualizar alguno sin que el compilador avise nada.
Con la jerarquia, agregar un modo es escribir una clase nueva que implementa
lo que hace falta; si falta algo pure virtual, no compila. El resto del
programa (Network, chargeItinerary, los reportes) no sabe que clases existen,
solo conoce la interfaz TransportMode, asi que agregar un modo no obliga a
tocar nada mas. Esa es la diferencia entre tener que abrir varios archivos
por cada cambio y simplemente sumar uno nuevo.

Reglas que se respetaron:
- nada de dynamic_cast, typeid, ni comparar serviceType() para decidir
  comportamiento
- un solo loop explicito en todo el programa, el for de chargeItinerary. Todo
  lo demas usa for_each, accumulate, count_if, find_if
- el literal 3820 aparece exactamente tres veces (MetroLine, TramLine,
  CableLine); TouristCable saca su tarifa de CableLine::baseFare()
