# Reto-5
Ciclo 2 MisionTic 2022 Programación básica

package controller;

//Estructuras de datos (colecciones)
import java.util.ArrayList;

//Modelos (acceso y objetos contenedores)
import model.dao.AsesorPorCiudadDao;
import model.dao.CompraPorProveedorDao;
import model.dao.ProyectosCasaCampestreDao;
import model.vo.AsesorPorCiudad;
import model.vo.CompraPorProveedor;
import model.vo.ProyectosCasaCampestre;


//Librerías para bases de datos
import java.sql.SQLException;

public class ControladorRequerimientosReto4 {       
    
    private final CompraPorProveedorDao compraPorProveedorDao;
    private final ProyectosCasaCampestreDao proyectosCasaCampestreDao;
    private final AsesorPorCiudadDao asesorPorCiudadDao;
    
    public ControladorRequerimientosReto4(){
        this.compraPorProveedorDao = new CompraPorProveedorDao();
        this.asesorPorCiudadDao = new AsesorPorCiudadDao();
        this.proyectosCasaCampestreDao = new ProyectosCasaCampestreDao();

    }

    public ArrayList<AsesorPorCiudad> consultarAsesorPorCiudad() throws SQLException {
        return this.asesorPorCiudadDao.rankingAsesorPorCiudad();
    }
    
    public ArrayList<CompraPorProveedor> consultarCompraPorProveedor() throws SQLException {
        return this.compraPorProveedorDao.rankingCompraPorProveedor();
    }

    public ArrayList<ProyectosCasaCampestre> consultarProyectosCasaCampestre() throws SQLException {
        return this.proyectosCasaCampestreDao.rankingProyectosCasaCampestres();
    }

}


//Clase para conexión
import util.JDBCUtilities;

//Encapsulamiento de los datos
import model.vo.AsesorPorCiudad;

public class AsesorPorCiudadDao {
    //Obtener los proyectos por Tipo en la ciudad de 
    public ArrayList<AsesorPorCiudad> rankingAsesorPorCiudad() throws SQLException {

        ArrayList<AsesorPorCiudad> respuesta = new ArrayList<AsesorPorCiudad>();
        Connection conexion = JDBCUtilities.getConnection();

        try{       

            String consulta =   "SELECT ID_Lider, Nombre, Primer_Apellido, Ciudad_Residencia "+
                                "FROM Lider "+
                                "WHERE Cargo = 'Asesor'"+
                                "ORDER BY Ciudad_Residencia ASC";


            PreparedStatement statement = conexion.prepareStatement(consulta);
            ResultSet resultSet = statement.executeQuery();

            //Recorrer los registros en los VO específicos
            while(resultSet.next()){
                AsesorPorCiudad asesorPorCiudad = new AsesorPorCiudad();
                asesorPorCiudad.setIdLider(resultSet.getInt("ID_Lider"));
                asesorPorCiudad.setNombre(resultSet.getString("Nombre"));
                asesorPorCiudad.setPrimerApellido(resultSet.getString("Primer_Apellido"));
                asesorPorCiudad.setCiudadResidencia(resultSet.getString("Ciudad_Residencia"));

                //Se agrega cada registro como un objeto del ArrayList que contiene la consulta
                respuesta.add(asesorPorCiudad);
            }

            resultSet.close();
            statement.close();

        }catch(SQLException e){
            System.err.println("Error consultando los proyectos de tipo Apartaestudio en Quibdo: "+e);
        }finally{
            if(conexion != null){
                conexion.close();
            }
        }

        //Retornar la colección de vo's
        return respuesta;
    }     
}

import model.vo.CompraPorProveedor;

public class CompraPorProveedorDao {
    
    public ArrayList<CompraPorProveedor> rankingCompraPorProveedor() throws SQLException {
        
        ArrayList<CompraPorProveedor> respuesta = new ArrayList<CompraPorProveedor>();
        Connection conexion = JDBCUtilities.getConnection();
        try{

            String consulta = "SELECT ID_Compra, Proveedor, Constructora, Banco_Vinculado, Ciudad " +
                                "FROM Compra c INNER JOIN Proyecto p " +
                                "ON c.ID_Proyecto = p.ID_Proyecto " +
                                "WHERE Ciudad = 'Salento' AND Proveedor = 'Homecenter'";


            PreparedStatement statement = conexion.prepareStatement(consulta);
            ResultSet resultSet = statement.executeQuery();

            //Recorrer los registros en los VO específicos
            while(resultSet.next()){
                CompraPorProveedor asesorPorCiudad = new CompraPorProveedor();
                asesorPorCiudad.setID_Compra(resultSet.getInt("ID_Compra"));
                asesorPorCiudad.setProveedor(resultSet.getString("Proveedor"));
                asesorPorCiudad.setConstructora(resultSet.getString("Constructora"));
                asesorPorCiudad.setBanco_Vinculado(resultSet.getString("Banco_Vinculado"));
                asesorPorCiudad.setCiudad(resultSet.getString("Ciudad"));

                //Se agrega cada registro como un objeto del ArrayList que contiene la consulta
                respuesta.add(asesorPorCiudad);
            }
            resultSet.close();
            statement.close();

        }catch(SQLException e){
            System.err.println("Error consultando las compras por proveedor: " + e);
        }finally{
            if(conexion != null){
                conexion.close();
            }
        }
        return respuesta;
    }
    
}

import model.vo.ProyectosCasaCampestre;

public class ProyectosCasaCampestreDao {
   
    public ArrayList<ProyectosCasaCampestre> rankingProyectosCasaCampestres() throws SQLException {
        
        ArrayList<ProyectosCasaCampestre> respuesta = new ArrayList<ProyectosCasaCampestre>();
        Connection conexion = JDBCUtilities.getConnection();
        try{

            String consulta =   "SELECT ID_Proyecto, Constructora, Numero_Habitaciones, Ciudad " + 
                                "FROM Proyecto " +
                                "WHERE Ciudad IN ('Santa Marta', 'Cartagena', 'Barranquilla') " +
                                "AND Clasificacion = 'Casa Campestre'";


            PreparedStatement statement = conexion.prepareStatement(consulta);
            ResultSet resultSet = statement.executeQuery();
            while(resultSet.next()){
                ProyectosCasaCampestre proyectoCasaCampestre = new ProyectosCasaCampestre();
                proyectoCasaCampestre.setID_Proyecto(resultSet.getInt("ID_Proyecto"));
                proyectoCasaCampestre.setConstructora(resultSet.getString("Constructora"));
                proyectoCasaCampestre.setNumero_Habitaciones(resultSet.getDouble("Numero_Habitaciones"));
                proyectoCasaCampestre.setCiudad(resultSet.getString("Ciudad"));

                //Se agrega cada registro como un objeto del ArrayList que contiene la consulta
                respuesta.add(proyectoCasaCampestre);
            }
                
            resultSet.close();
            statement.close();

        }catch(SQLException e){
            System.err.println("Error consultando los proyectos: " + e);
        }finally{
            if(conexion != null){
                conexion.close();
            }
        }
        return respuesta;
    }
        
}

    public AsesorPorCiudad() {
    }

    public AsesorPorCiudad(Integer idLider, String nombre, String primerApellido, String ciudadResidencia) {
        IdLider = idLider;
        this.nombre = nombre;
        this.primerApellido = primerApellido;
        this.ciudadResidencia = ciudadResidencia;
    }

    public Integer getIdLider() {
        return IdLider;
    }

    public void setIdLider(Integer idLider) {
        IdLider = idLider;
    }

    public String getNombre() {
        return nombre;
    }

    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    public String getPrimerApellido() {
        return primerApellido;
    }

    public void setPrimerApellido(String primerApellido) {
        this.primerApellido = primerApellido;
    }

    public String getCiudadResidencia() {
        return ciudadResidencia;
    }

    public void setCiudadResidencia(String ciudadResidencia) {
        this.ciudadResidencia = ciudadResidencia;
    }


    
}

public class CompraPorProveedor {
    
    private Integer ID_Compra;
    private String Proveedor;
    private String Constructora;
    private String Banco_Vinculado;
    private String Ciudad;
    
    public CompraPorProveedor() {
    }

    public CompraPorProveedor(Integer iD_Compra, String proveedor, String constructora, String banco_Vinculado,
            String ciudad) {
        ID_Compra = iD_Compra;
        Proveedor = proveedor;
        Constructora = constructora;
        Banco_Vinculado = banco_Vinculado;
        Ciudad = ciudad;
    }

    public Integer getID_Compra() {
        return ID_Compra;
    }

    public void setID_Compra(Integer iD_Compra) {
        ID_Compra = iD_Compra;
    }

    public String getProveedor() {
        return Proveedor;
    }

    public void setProveedor(String proveedor) {
        Proveedor = proveedor;
    }

    public String getConstructora() {
        return Constructora;
    }

    public void setConstructora(String constructora) {
        Constructora = constructora;
    }

    public String getBanco_Vinculado() {
        return Banco_Vinculado;
    }

    public void setBanco_Vinculado(String banco_Vinculado) {
        Banco_Vinculado = banco_Vinculado;
    }

    public String getCiudad() {
        return Ciudad;
    }

    public void setCiudad(String ciudad) {
        Ciudad = ciudad;
    }
    
    
}

public class ProyectosCasaCampestre {
    private Integer ID_Proyecto;
    private String Constructora; 
    private Double Numero_Habitaciones;
    private String Ciudad;
    
    public ProyectosCasaCampestre() {
    }

    public ProyectosCasaCampestre(Integer iD_Proyecto, String constructora, Double numero_Habitaciones, String ciudad) {
        ID_Proyecto = iD_Proyecto;
        Constructora = constructora;
        Numero_Habitaciones = numero_Habitaciones;
        Ciudad = ciudad;
    }

    public Integer getID_Proyecto() {
        return ID_Proyecto;
    }

    public void setID_Proyecto(Integer iD_Proyecto) {
        ID_Proyecto = iD_Proyecto;
    }

    public String getConstructora() {
        return Constructora;
    }

    public void setConstructora(String constructora) {
        Constructora = constructora;
    }

    public Double getNumero_Habitaciones() {
        return Numero_Habitaciones;
    }

    public void setNumero_Habitaciones(Double numero_Habitaciones) {
        Numero_Habitaciones = numero_Habitaciones;
    }

    public String getCiudad() {
        return Ciudad;
    }

    public void setCiudad(String ciudad) {
        Ciudad = ciudad;
    }

    
}

import java.io.File;//Para verificación de longitud de base de datos

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class JDBCUtilities {

    //Atributos de clase para gestión de conexión con la base de datos    
    private static final String UBICACION_BD = "ProyectosConstruccion.db";    

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:sqlite:" + UBICACION_BD;        
        return DriverManager.getConnection(url);
    }

    public static boolean estaVacia(){
        File archivo = new File(JDBCUtilities.UBICACION_BD);
        return archivo.length() == 0;
    }
}

import java.awt.EventQueue;

import view.VistaRequerimientosReto4;

public class App {
    public static void main(String[] args) {

        // Ejecutar Swing en otro hilo
        EventQueue.invokeLater(new Runnable() {
            @Override
            public void run() {
                try {
                    VistaRequerimientosReto4 frame = new VistaRequerimientosReto4();
                    frame.setVisible(true);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
    }
}
