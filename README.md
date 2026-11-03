# -taller_Taller-Herencia-polimorfismo-abstractas-e-interfaces

package envios.modelo;

public interface Asegurable {
    double valorSeguro();
}

package envios.modelo;

public abstract class Vehiculo {
    private String placa;
    private int capacidadKg;

    public Vehiculo(String placa, int capacidadKg) {
        this.placa = placa;
        this.capacidadKg = capacidadKg;
    }

    public String getPlaca() {
        return placa;
    }

    public int getCapacidadKg() {
        return capacidadKg;
    }

    public String describir() {
        return placa + " (" + capacidadKg + " kg)";
    }

    public abstract double tarifaPorKm();

    public double costoEnvio(double km) {
        return km * tarifaPorKm();
    }

    public double costoEnvio(double km, boolean urgente) {
        double costoBase = costoEnvio(km);
        return urgente ? costoBase * 1.5 : costoBase;
    }
}

package envios.modelo;

public class Moto extends Vehiculo implements Asegurable {
    private int cilindraje;

    public Moto(String placa, int capacidadKg, int cilindraje) {
        super(placa, capacidadKg);
        this.cilindraje = cilindraje;
    }

    @Override
    public String describir() {
        return super.describir() + ", moto de " + cilindraje + " cc";
    }

    @Override
    public double tarifaPorKm() {
        return 1200;
    }

    @Override
    public double valorSeguro() {
        return 80000 + cilindraje * 500;
    }
}

package envios.modelo;

public class Camion extends Vehiculo implements Asegurable {
    private int ejes;

    public Camion(String placa, int capacidadKg, int ejes) {
        super(placa, capacidadKg);
        this.ejes = ejes;
    }

    public int getEjes() {
        return ejes;
    }

    @Override
    public String describir() {
        return super.describir() + ", camión de " + ejes + " ejes";
    }

    @Override
    public double tarifaPorKm() {
        return 3000 + 1000 * ejes;
    }

    @Override
    public double valorSeguro() {
        return 400000 * ejes;
    }
}

package envios.modelo;

public class Bicicleta extends Vehiculo {
    public Bicicleta(String placa, int capacidadKg) {
        super(placa, capacidadKg);
    }

    @Override
    public String describir() {
        return super.describir() + ", bicicleta eléctrica";
    }

    @Override
    public double tarifaPorKm() {
        return 600;
    }
}

package envios.modelo;

public class Bodega implements Asegurable {
    private String nombre;
    private int metros;

    public Bodega(String nombre, int metros) {
        this.nombre = nombre;
        this.metros = metros;
    }

    @Override
    public double valorSeguro() {
        return metros * 2000;
    }
}

package envios.modelo;

import java.util.ArrayList;

public class Flota {
    private String nombre;
    private ArrayList<Vehiculo> vehiculos;

    public Flota(String nombre) {
        this.nombre = nombre;
        this.vehiculos = new ArrayList<>();
    }

    public boolean agregar(Vehiculo vehiculo) {
        if (vehiculo == null || vehiculos.contains(vehiculo)) {
            return false;
        }
        return vehiculos.add(vehiculo);
    }

    public void mostrar() {
        System.out.println(nombre + ":");
        for (Vehiculo v : vehiculos) {
            System.out.println(v.describir());
        }
    }

    public int cantidadCamiones() {
        int contador = 0;
        for (Vehiculo v : vehiculos) {
            if (v instanceof Camion) {
                contador++;
            }
        }
        return contador;
    }

    public Vehiculo masEconomico(int pesoKg) {
        Vehiculo vehiculoOptimo = null;
        for (Vehiculo v : vehiculos) {
            if (v.getCapacidadKg() >= pesoKg) {
                if (vehiculoOptimo == null || v.tarifaPorKm() < vehiculoOptimo.tarifaPorKm()) {
                    vehiculoOptimo = v;
                }
            }
        }
        return vehiculoOptimo;
    }

    public double totalSeguros() {
        double acumulado = 0;
        for (Vehiculo v : vehiculos) {
            if (v instanceof Asegurable) {
                acumulado += ((Asegurable) v).valorSeguro();
            }
        }
        return acumulado;
    }
}

package envios.app;

import envios.modelo.*;
import java.util.ArrayList;

public class Main {
    public static void main(String[] args) {
        Moto m1 = new Moto("MZL12A", 20, 150);
        Camion c1 = new Camion("WFT345", 8000, 2);
        Moto m2 = new Moto("KJH78B", 25, 200);

        Flota flota = new Flota("Envíos Caldas");
        flota.agregar(m1);
        flota.agregar(c1);
        flota.agregar(m2);

        System.out.println(flota.agregar(m1));

        System.out.println(m1.costoEnvio(12));
        System.out.println(c1.costoEnvio(12));

        Bicicleta b1 = new Bicicleta("BIC001", 10);
        flota.agregar(b1);

        flota.mostrar();

        System.out.println(flota.masEconomico(8) != null ? flota.masEconomico(8).getPlaca() : "null");
        System.out.println(flota.masEconomico(22) != null ? flota.masEconomico(22).getPlaca() : "null");
        System.out.println(flota.masEconomico(500) != null ? flota.masEconomico(500).getPlaca() : "null");
        System.out.println(flota.masEconomico(9000) != null ? flota.masEconomico(9000).getPlaca() : "null");

        System.out.println(m1.valorSeguro());
        System.out.println(c1.valorSeguro());
        System.out.println(flota.totalSeguros());

        ArrayList<Asegurable> asegurables = new ArrayList<>();
        asegurables.add(c1);
        asegurables.add(new Bodega("Bodega Chipre", 300));

        double suma = 0;
        for (Asegurable a : asegurables) {
            System.out.println(a.valorSeguro());
            suma += a.valorSeguro();
        }
        System.out.println(suma);
    }
}



1. Análisis de Errores de Compilación
Vehiculo is abstract; cannot be instantiated

Motivo: Al ser Vehiculo una clase abstracta, el compilador prohíbe explícitamente la creación de instancias directas mediante la palabra reservada new.

Bicicleta is not abstract and does not override abstract method tarifaPorKm() in Vehiculo

Motivo: Toda clase concreta que extienda de una clase abstracta está obligada a implementar la totalidad de los métodos abstractos heredados.

2. Casos Especiales de Código
Acceso a métodos específicos tras un cast (getEjes()):

Java
Vehiculo w = c1;
if (w instanceof Camion) {
    System.out.println(((Camion) w).getEjes());
}
Explicación: Como la variable w es de tipo genérico Vehiculo, el compilador desconoce el método getEjes(). Es necesario verificar la instancia con instanceof y realizar un cast explícito a Camion.

Sobrecarga (Overloading):

Ocurre en la clase Vehiculo con costoEnvio(double km) y costoEnvio(double km, boolean urgente), ya que comparten identificador pero varían en sus firmas de parámetros.

Modificación de describir a describr sin @Override:

Al no coincidir el nombre con el método padre, el compilador lo interpreta como un método totalmente nuevo y propio de Bicicleta. Por ende, al invocarlo, recurre al comportamiento heredado de Vehiculo imprimiendo únicamente los datos básicos. Con @Override, el entorno detectaría el error tipográfico y detendría la compilación.

3. Salida Esperada en Consola (Fragmento de Ejemplo)
Plaintext
AAA11A (20 kg), moto de 100 cc
12000.0
true
false
100000.0
4. Conceptos Teóricos y Arquitectura POO
Integración de Bicicleta en la flota:

Operativo gracias al polimorfismo, permitiendo que estructuras tipadas como Vehiculo alberguen objetos derivados que responden dinámicamente a sus contratos de métodos.

Diferenciación de instanciación:

Vehiculo centraliza el comportamiento genérico vedando su instanciación, mientras que Moto define una entidad concreta y autónoma.

Implementación transversal (Bodega y Asegurable):

Demuestra que las interfaces logran estandarizar comportamientos específicos (como calcular seguros) en clases totalmente desacopladas de una jerarquía de herencia tradicional.

Diferencia entre super(...) y super.describir():

super(...) invoca al constructor de la superclase para inicializar atributos heredados. super.describir() ejecuta directamente la lógica definida en el método de la clase superior para extenderla.

Pilares de la Programación Orientada a Objetos:

POO: Representación de entidades del mundo real mediante atributos y comportamientos acoplados.

Abstracción: Definición de plantillas y contratos mediante clases y métodos abstractos.

Encapsulamiento: Ocultamiento del estado interno (private) gestionado mediante interfaces públicas (getters).

Herencia: Mecanismo de reutilización jerárquica de código.

Polimorfismo: Resolución dinámica de métodos según el tipo real del objeto en tiempo de ejecución.
