// Clase Empleado
import java.util.LinkedList;

class Empleado {
    private String nombre;
    private String dni;
    private String puesto;

    public Empleado(String nombre, String dni, String puesto) {
        this.nombre = nombre;
        this.dni = dni;
        this.puesto = puesto;
    }

    public String getNombre() {
        return nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getPuesto() {
        return puesto;
    }

    @Override
    public String toString() {
        return "Empleado{" +
               "nombre='" + nombre + '\'' +
               ", dni='" + dni + '\'' +
               ", puesto='" + puesto + '\'' +
               '}';
    }
}

// Clase HashNode
class HashNode<K, V> {
    K key;
    V value;

    public HashNode(K key, V value) {
        this.key = key;
        this.value = value;
    }
}

// Clase HashTable
class HashTable<K, V> {
    private LinkedList<HashNode<K, V>>[] buckets;
    private int capacity;
    private int size;
    private final double MAX_LOAD_FACTOR = 0.75;
    private final double MIN_LOAD_FACTOR = 0.25;

    public HashTable(int initialCapacity) {
        this.capacity = initialCapacity;
        this.buckets = new LinkedList[capacity];
        for (int i = 0; i < capacity; i++) {
            buckets[i] = new LinkedList<>();
        }
        this.size = 0;
    }

    private int getBucketIndex(K key) {
        return Math.abs(key.hashCode()) % capacity;
    }

    public void put(K key, V value) {
        int index = getBucketIndex(key);
        for (HashNode<K, V> node : buckets[index]) {
            if (node.key.equals(key)) {
                node.value = value;
                return;
            }
        }
        buckets[index].add(new HashNode<>(key, value));
        size++;
        checkLoadFactor();
    }

    public V get(K key) {
        int index = getBucketIndex(key);
        for (HashNode<K, V> node : buckets[index]) {
            if (node.key.equals(key)) {
                return node.value;
            }
        }
        return null;
    }

    public void remove(K key) {
        int index = getBucketIndex(key);
        for (HashNode<K, V> node : buckets[index]) {
            if (node.key.equals(key)) {
                buckets[index].remove(node);
                size--;
                checkLoadFactor();
                return;
            }
        }
    }

    private void checkLoadFactor() {
        double loadFactor = (double) size / capacity;
        if (loadFactor > MAX_LOAD_FACTOR) {
            resize(capacity * 2);
        } else if (loadFactor < MIN_LOAD_FACTOR && capacity > 8) {
            resize(capacity / 2);
        }
    }

    private void resize(int newCapacity) {
        LinkedList<HashNode<K, V>>[] oldBuckets = buckets;
        capacity = newCapacity;
        buckets = new LinkedList[capacity];
        for (int i = 0; i < capacity; i++) {
            buckets[i] = new LinkedList<>();
        }
        size = 0;

        for (LinkedList<HashNode<K, V>> bucket : oldBuckets) {
            for (HashNode<K, V> node : bucket) {
                put(node.key, node.value);
            }
        }
    }

    public void printTable() {
        for (int i = 0; i < capacity; i++) {
            System.out.print("Bucket " + i + ": ");
            for (HashNode<K, V> node : buckets[i]) {
                System.out.print("[" + node.key + " = " + node.value + "] ");
            }
            System.out.println();
        }
    }
}

// Clase Main
public class Main {
    public static void main(String[] args) {
        HashTable<String, Empleado> empleados = new HashTable<>(8);

        empleados.put("Juan", new Empleado("Juan Pérez", "12345678A", "Contador"));
        empleados.put("Ana", new Empleado("Ana Torres", "87654321B", "Administradora"));
        empleados.put("Luis", new Empleado("Luis Méndez", "11223344C", "Gerente"));

        empleados.printTable();

        System.out.println("\nBuscando a Ana:");
        System.out.println(empleados.get("Ana"));

        System.out.println("\nEliminando a Luis...");
        empleados.remove("Luis");
        empleados.printTable();
    }
}
