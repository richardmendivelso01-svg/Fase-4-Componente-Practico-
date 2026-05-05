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
 */
using System;
using System.Collections.Generic;
using System.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
namespace SistemaAgro_UNAD_Fase4
{
    // Modelo de datos alineado con tu proyecto de grado
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
                Console.WriteLine("==================================================");
                Console.WriteLine("1. Registrar Producto (Inventario)");
                Console.WriteLine("2. Ver Stock Actual");
                Console.WriteLine("3. Registrar Venta (Comercialización)");
                Console.WriteLine("4. Reporte de Ventas");
                Console.WriteLine("5. Salir");
                Console.Write("\nSeleccione una opción: ");

                string opcion = Console.ReadLine();
                switch (opcion)
                {
                    case "1": MenuRegistrar(); break;
                    case "2": MenuVerStock(); break;
                    case "3": MenuVenta(); break;
                    case "4": MenuReporte(); break;
                    case "5": continuar = false; break;
                    default: Console.WriteLine("Opción no válida."); Pausar(); break;
                }
            }
        }

        static void MenuRegistrar()
        {
            Console.WriteLine("\n--- Nuevo Registro de Cosecha ---");
            Producto p = new Producto();
            p.Id = inventario.Count + 1;

            Console.Write("Nombre del producto: ");
            p.Nombre = Console.ReadLine();

            // Usamos TryParse para que no se rompa si el usuario escribe letras
            p.Stock = LeerDouble("Cantidad en Stock: ");

            Console.Write("Unidad de medida (kg, bulto, etc): ");
            p.Unidad = Console.ReadLine();

            p.PrecioUnitario = LeerDecimal("Precio unitario de venta: ");

            inventario.Add(p);
            Console.WriteLine("\n[EXITO] Producto registrado correctamente.");
            Pausar();
        }

        static void MenuVerStock()
        {
            Console.WriteLine("\n--- Estado de Inventario Actual ---");
            // Ajustamos el espaciado para que las columnas coincidan
            Console.WriteLine("{0,-5} | {1,-15} | {2,-10} | {3,-10} | {4,-12}", "ID", "Nombre", "Stock", "Unidad", "Precio Unit.");
            Console.WriteLine("----------------------------------------------------------------------");

            foreach (var p in inventario)
            {
                Console.WriteLine("{0,-5} | {1,-15} | {2,-10} | {3,-10} | ${4,-12:N0}",
                    p.Id, p.Nombre, p.Stock, p.Unidad, p.PrecioUnitario);
            }
            Pausar();
        }

        static void MenuVenta()
        {
            Console.WriteLine("\n--- Registrar Venta (Comercialización) ---");
            if (inventario.Count == 0) { Console.WriteLine("No hay productos registrados."); Pausar(); return; }

            MenuVerStock(); // Mostrar lista para ver el ID

            int id = (int)LeerDouble("\nIngrese el ID (número) del producto a vender: ");
            var prod = inventario.FirstOrDefault(x => x.Id == id);

            if (prod != null)
            {
                double cant = LeerDouble($"Cantidad a vender de {prod.Nombre} (Máx {prod.Stock}): ");

                if (cant <= prod.Stock && cant > 0)
                {
                    prod.Stock -= cant;
                    Venta v = new Venta
                    {
                        Fecha = DateTime.Now,
                        Producto = prod.Nombre,
                        Cantidad = cant,
                        Total = (decimal)cant * prod.PrecioUnitario
                    };
                    historicoVentas.Add(v);
                    Console.WriteLine($"\n[VENTA REALIZADA] Total cobrado: ${v.Total:N0}");
                }
                else
                {
                    Console.WriteLine("[ERROR] Cantidad no válida o superior al stock.");
                }
            }
            else
            {
                Console.WriteLine("[ERROR] El ID ingresado no existe.");
            }
            Pausar();
        }

        static void MenuReporte()
        {
            Console.WriteLine("\n--- Reporte Consolidado de Ventas ---");
            decimal totalGeneral = 0;
            foreach (var v in historicoVentas)
            {
                Console.WriteLine($"{v.Fecha:dd/MM/yyyy} | {v.Producto} | Cant: {v.Cantidad} | Subtotal: ${v.Total:N0}");
                totalGeneral += v.Total;
            }
            Console.WriteLine($"\nTOTAL INGRESOS: ${totalGeneral:N0}");
            Pausar();
        }

        // MÉTODOS DE APOYO PARA EVITAR CRASHES
        static double LeerDouble(string mensaje)
        {
            double resultado;
            while (true)
            {
                Console.Write(mensaje);
                if (double.TryParse(Console.ReadLine(), out resultado)) return resultado;
                Console.WriteLine("[!] Error: Ingrese un valor numérico válido.");
            }
        }

        static decimal LeerDecimal(string mensaje)
        {
            decimal resultado;
            while (true)
            {
                Console.Write(mensaje);
                if (decimal.TryParse(Console.ReadLine(), out resultado)) return resultado;
                Console.WriteLine("[!] Error: Ingrese un valor numérico (dinero) válido.");
            }
        }

        static void Pausar() { Console.WriteLine("\nPresione cualquier tecla para continuar..."); Console.ReadKey(); }
    }
}
