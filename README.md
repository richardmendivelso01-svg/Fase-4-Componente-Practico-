# Fase-4-Componente-Practico-
implementar la metodología de desarrollo, análisis de requerimientos en el desarrollo del prototipo funcional que debe tener un nivel de maduración tecnológica TRL5 

/* Fase 4 Componente Practico 
 Universidad Nacional Abierta y a Distancia UNAD
Proyecto de Grado: Sistema de Gestión Agrícola para la Comercialización de Cosechas - UNAD (TRL 5)

 Tutor: 

Orlando Gomez Barbosa 

 Integrantes: 

Richard Sneyder Mendivelso Romero 
Rocio Zenit Bautista Rojas
Kelsen German Gongora Zambrano 
Yulieth Valentina Suarez 

using System;
using System.Collections.Generic;
using System.Linq;

using System;
using System.Collections.Generic;
using System.Linq;

namespace SistemaAgro_UNAD_Fase4
{
    public class Producto
    {
        public int Id { get; set; }
        public string Nombre { get; set; }
        public double Stock { get; set; }
        public string Unidad { get; set; }
        public decimal PrecioUnitario { get; set; }
    }

    public class Venta
    {
        public DateTime Fecha { get; set; }
        public string Producto { get; set; }
        public double Cantidad { get; set; }
        public decimal Total { get; set; }
    }

    class Program
    {
        static List<Producto> inventario = new List<Producto>();
        static List<Venta> historicoVentas = new List<Venta>();

        static void Main(string[] args)
        {
            bool continuar = true;
            while (continuar)
            {
                Console.Clear();
                Console.WriteLine("==================================================");
                Console.WriteLine("   SISTEMA DE GESTIÓN AGRÍCOLA - UNAD (TRL 5)     ");
                Console.WriteLine("   MÓDULO DE INGENIERÍA CON CRUD COMPLETO         ");
                Console.WriteLine("==================================================");
                Console.WriteLine("1. [C] CREATE - Registrar Producto");
                Console.WriteLine("2. [R] READ   - Ver Inventario / Stock");
                Console.WriteLine("3. [U] UPDATE - Modificar Producto existente");
                Console.WriteLine("4. [D] DELETE - Eliminar Producto");
                Console.WriteLine("5. REGISTRAR VENTA (Comercialización)");
                Console.WriteLine("6. REPORTE DE VENTAS");
                Console.WriteLine("7. Salir");
                Console.Write("\nSeleccione una opción: ");

                string opcion = Console.ReadLine();
                switch (opcion)
                {
                    case "1": MenuRegistrar(); break;
                    case "2": MenuVerStock(); Pausar(); break;
                    case "3": MenuActualizar(); break;
                    case "4": MenuEliminar(); break;
                    case "5": MenuVenta(); break;
                    case "6": MenuReporte(); break;
                    case "7": continuar = false; break;
                    default: Console.WriteLine("Opción no válida."); Pausar(); break;
                }
            }
        }

        // --- C: CREATE ---
        static void MenuRegistrar()
        {
            Console.WriteLine("\n--- Nuevo Registro de Cosecha ---");
            Producto p = new Producto();
            p.Id = inventario.Count > 0 ? inventario.Max(x => x.Id) + 1 : 1;

            Console.Write("Nombre del producto: ");
            p.Nombre = Console.ReadLine();
            p.Stock = LeerDouble("Cantidad en Stock: ");
            Console.Write("Unidad de medida (kg, bulto, etc): ");
            p.Unidad = Console.ReadLine();
            p.PrecioUnitario = LeerDecimal("Precio unitario de venta: ");

            inventario.Add(p);
            Console.WriteLine("\n[EXITO] Producto registrado correctamente.");
            Pausar();
        }

        // --- R: READ ---
        static void MenuVerStock()
        {
            Console.WriteLine("\n--- Estado de Inventario Actual ---");
            Console.WriteLine("{0,-5} | {1,-15} | {2,-10} | {3,-10} | {4,-12}", "ID", "Nombre", "Stock", "Unidad", "Precio Unit.");
            Console.WriteLine("----------------------------------------------------------------------");
            foreach (var p in inventario)
            {
                Console.WriteLine("{0,-5} | {1,-15} | {2,-10} | {3,-10} | ${4,-12:N0}",
                    p.Id, p.Nombre, p.Stock, p.Unidad, p.PrecioUnitario);
            }
        }

        // --- U: UPDATE ---
        static void MenuActualizar()
        {
            MenuVerStock();
            Console.Write("\nIngrese el ID del producto a modificar: ");
            if (int.TryParse(Console.ReadLine(), out int id))
            {
                var p = inventario.FirstOrDefault(x => x.Id == id);
                if (p != null)
                {
                    Console.Write($"Nuevo nombre ({p.Nombre}): ");
                    string nuevoNombre = Console.ReadLine();
                    if (!string.IsNullOrEmpty(nuevoNombre)) p.Nombre = nuevoNombre;

                    p.Stock = LeerDouble($"Nuevo stock ({p.Stock}): ");
                    p.PrecioUnitario = LeerDecimal($"Nuevo precio ({p.PrecioUnitario}): ");

                    Console.WriteLine("\n[EXITO] Producto actualizado.");
                }
                else Console.WriteLine("[!] ID no encontrado.");
            }
            else Console.WriteLine("[!] Entrada inválida.");
            Pausar();
        }

        // --- D: DELETE ---
        static void MenuEliminar()
        {
            MenuVerStock();
            Console.Write("\nID del producto a eliminar: ");
            if (int.TryParse(Console.ReadLine(), out int id))
            {
                int eliminados = inventario.RemoveAll(x => x.Id == id);
                if (eliminados > 0) Console.WriteLine("[OK] Producto eliminado satisfactoriamente.");
                else Console.WriteLine("[!] No se encontró ningún producto con ese ID.");
            }
            else Console.WriteLine("[!] Error: Debe ingresar un número de ID válido.");
            Pausar();
        }

        // --- LÓGICA DE NEGOCIO (VENTAS) ---
        static void MenuVenta()
        {
            Console.WriteLine("\n--- Registrar Venta (Comercialización) ---");
            if (inventario.Count == 0) { Console.WriteLine("No hay productos registrados."); Pausar(); return; }
            MenuVerStock();

            Console.Write("\nIngrese el ID del producto a vender: ");
            if (int.TryParse(Console.ReadLine(), out int id))
            {
                var prod = inventario.FirstOrDefault(x => x.Id == id);
                if (prod != null)
                {
                    double cant = LeerDouble($"Cantidad a vender (Máx {prod.Stock}): ");
                    if (cant <= prod.Stock && cant > 0)
                    {
                        prod.Stock -= cant;
                        historicoVentas.Add(new Venta
                        {
                            Fecha = DateTime.Now,
                            Producto = prod.Nombre,
                            Cantidad = cant,
                            Total = (decimal)cant * prod.PrecioUnitario
                        });
                        Console.WriteLine($"\n[VENTA REALIZADA] Total: ${((decimal)cant * prod.PrecioUnitario):N0}");
                    }
                    else Console.WriteLine("[ERROR] Cantidad no válida.");
                }
                else Console.WriteLine("[ERROR] ID no existe.");
            }
            else Console.WriteLine("[!] Entrada inválida.");
            Pausar();
        }

        static void MenuReporte()
        {
            Console.WriteLine("\n--- Reporte de Ventas ---");
            decimal total = 0;
            foreach (var v in historicoVentas)
            {
                Console.WriteLine($"{v.Fecha:dd/MM/yyyy} | {v.Producto} | Cant: {v.Cantidad} | ${v.Total:N0}");
                total += v.Total;
            }
            Console.WriteLine($"\nTOTAL INGRESOS: ${total:N0}");
            Pausar();
        }

        // --- VALIDACIONES ---
        static double LeerDouble(string mensaje)
        {
            double r;
            while (true)
            {
                Console.Write(mensaje);
                if (double.TryParse(Console.ReadLine(), out r)) return r;
                Console.WriteLine("[!] Ingrese un número válido.");
            }
        }

        static decimal LeerDecimal(string mensaje)
        {
            decimal r;
            while (true)
            {
                Console.Write(mensaje);
                if (decimal.TryParse(Console.ReadLine(), out r)) return r;
                Console.WriteLine("[!] Ingrese un precio válido.");
            }
        }

        static void Pausar() { Console.WriteLine("\nPresione cualquier tecla..."); Console.ReadKey(); }
    }
}
