# TechCore Sales Analytics | Power BI & Data Modeling

## Project Context and Student Role

TechCore is a retail store chain specializing in the sale and distribution of computers across all tiers. It has hired DataVision Analytics to centralize and analyze its sales information at the national level. The objective is to transform raw billing data into structured information, create a relational model, and develop an interactive Power BI dashboard to extract strategic conclusions. For this purpose, a technical challenge has been designed for candidates applying for the Junior Data Scientist role, aiming to demonstrate their ability to integrate multiple data sources, analyze them, and generate actionable insights.

As part of the selection process for the Junior Data Analyst role, DataVision Analytics has designed a technical challenge in which the candidate must complete the 3 phases of the project.

### Project Components:

```
techcore-dashboard/
├── data/
│   ├── raw/
│   │   └── ventas.csv
│   └── processed/
│       └── modeloVentas.xlsx
│       └── ventasTransformed.csv
└── docs/
│   └── Conclusiones_Recomendaciones.md
├── notebooks/
│   └── 02_Modelo_Relacional.ipynb
└── powerbi/
│   └── 01_Limpieza_Transformación.pbix
│   └── 03_Dashboad_PowerBI.pbix
└── README.pdf
```

## Steps and Considerations for the Complete Development of the Project

## Phase 1

#### Transformation and Standardization using Power Query

A preliminary analysis is conducted to verify how the data is received:

<img width="979" height="202" alt="image" src="https://github.com/user-attachments/assets/74cc8872-18cd-456d-8127-09771881dfec" />

<img width="979" height="187" alt="image-1" src="https://github.com/user-attachments/assets/eddc0465-0722-4d77-a88b-2a7330d4353d" />

<img width="979" height="199" alt="image-2" src="https://github.com/user-attachments/assets/8614e7d9-f2d3-49d2-b7cb-5123ed10fca6" />

<img width="979" height="197" alt="image-3" src="https://github.com/user-attachments/assets/85e49008-74de-40b0-b46f-757550a0054e" />


<img width="979" height="197" alt="image-4" src="https://github.com/user-attachments/assets/7519f922-69c8-49a7-94b8-577fa88063c9" />

 <img width="979" height="196" alt="image-5" src="https://github.com/user-attachments/assets/b8346485-78fa-4a81-8810-12555f62f69f" />


Based on this information, data transformation and cleaning are carried out as follows:

1. Column names are standardized into PascalCase format. Example: NombreColumna, except for VentaID, which will keep its name unchanged as it is the primary key.

2. The data type for the columns PrecioUnitarioProducto1/2/3, SubtotalProducto1/2/3, and TotalVenta is modified to Decimal number to avoid conflicts with Python if fixed decimal numbers or currency formatting are required later.

3. 
- Duplicate removal is performed on VentaID, as it serves as the unique sales identifier.

- For the TelefonoCliente column, it will be kept as suggested by E.164[1]: [ + ] [ Country Code ] [ Significant National Number ].

- All vowel accents are removed in the SucursalNombre, CiudadSucursal, VendedorNombre, and ClienteNombre columns to facilitate and standardize data search and analysis.

- Per project requirements, capitalizing the first letter of each word (Capitalize Each Word) is adopted as the standard for text data.

- For the Sucursal column, spaces are removed to reduce false categories and unify matches.

    <img width="371" height="212" alt="image-6" src="https://github.com/user-attachments/assets/1850dac5-fd18-42e4-a23f-645322222721" />

- Handling of Missing or Null Values:

    - For the city case, the city written in SucursalNombre will be taken as reference to replace the original city column, ensuring all city data is complete and consistent.


    - The brand is extracted from NombreProducto to replace the MarcaProducto column, as the latter contains spelling errors and empty values.

    - For EmailCliente, TelefonoCliente, MetodoPago, and second and third products (which are optional), blank values will be considered as "No registrado".

    - For numeric columns involving product prices, quantities, and discounts, null values are replaced with 0 to prevent future conflicts in mathematical operations.

Clean Dataset Ready for Work

<img width="979" height="201" alt="image-7" src="https://github.com/user-attachments/assets/03918653-de8d-46bd-b136-d42ba56a8346" />

<img width="979" height="183" alt="image-8" src="https://github.com/user-attachments/assets/b292016a-15c7-42e3-8016-87d51e0ad09d" />

<img width="979" height="199" alt="image-9" src="https://github.com/user-attachments/assets/5d22c42b-123c-4b36-9093-bbd5504b2fce" />

<img width="979" height="195" alt="image-10" src="https://github.com/user-attachments/assets/7b0b5197-76c2-4203-8dc3-a5a1e647e9ca" />

<img width="979" height="194" alt="image-11" src="https://github.com/user-attachments/assets/34770e3f-1f0b-4f03-a91b-03430ab13d51" />

<img width="979" height="194" alt="image-12" src="https://github.com/user-attachments/assets/fc6be49f-6bb9-4c4b-8815-467348809428" />


The transformed table data is copied into a text notebook and saved as ventasTransformed.csv. This file is stored inside the Avance 2 folder for subsequent use in Python.

## Phase 2

For the creation of the relational model, it is based on a star schema structure with a snowflake hybrid approach for the branch and city entities. Unmodified fields entering the new model, primary keys, foreign keys, and calculated values are identified. The following ER diagram is created for this case:

<img width="975" height="673" alt="image-13" src="https://github.com/user-attachments/assets/00fc8e1f-c86d-4f3c-b6fc-3a40eac776e1" />

Based on the diagram, primary keys are identified first in each table, followed sequentially by their corresponding foreign keys and their target table directions.

### Key Considerations for Tables:

- For float type columns and HoraVenta, formatting is specified when reading ventasTransformed.csv so Python does not unintentionally alter data types.

- Loops and iterations are avoided to optimize data processing performance.

- To construct the relational model, tables are created in order from highest to lowest independence: Ciudad, Vendedores, Clientes, Productos, Fechas, Sucursales, Facturas, and finally Detalles Facturas.

- Unique IDs are assigned once unique values are identified for each table. For practical purposes, a unique incremental position ID is assigned, with the exception of FechaID, which utilizes the YYYYMMDD format as its unique identifier.

- In the Productos table, purchase products 1, 2, and 3 are concatenated so that no product is left out. IDs are assigned after omitting "No registrado" values, unified under the names: "ProductoID", "NombreProducto", "MarcaProducto", and "PrecioUnitario".

- The previous methodology is applied to build the Detalle Factura table.

- To avoid overloading the invoices table, a Fechas table is created containing all related date information: Año, MesNumero, MesNombre, DiaNumero, DiaNombre, Trimestre, and EsFinDeSemana. This facilitates downstream analysis when needed.

- Foreign key references are retrieved using the .merge() function.

- Created tables are exported into an Excel file named modeloVentas.xlxs, which serves as the input for Phase 3.

## Phase 3

Once modeloVentas.xlxs is imported into Power BI, relationships between tables are verified regarding direction and cardinality. For further details, refer to Avance_3_Dashboad_PowerBI in the Model view tab.

<img width="975" height="695" alt="image-14" src="https://github.com/user-attachments/assets/7fb251f0-8dbb-48fc-aa1e-6a2d797f7b4b" />


Key business and required DAX measures were created:

- Total Sales: Accumulated revenue from business sales.

- Average Ticket: Average spending per customer invoice or transaction.

- % Share by City or Category: Contribution or weight of a city relative to overall revenue.

In addition, the following charts were generated for analysis:

- A geographic map displaying sales by city.

- A bar chart showing sales by product brand (Lenovo, Acer, Asus, etc.).

- A timeline depicting monthly sales evolution.

- KPI Cards highlighting global figures such as total sales, average ticket, quantity of products sold, and total customer volume.

For age range segmentation, target focus groups were established based on average age brackets corresponding to higher education / first employment (18–25 years), established professionals (26–40 years), and financial consolidation (41–51 years).

For full findings and conclusions, please refer to *03_Dashboad_PowerBI.pbix.*

Note: If desire to use the dashboard in local computer modify the parameter "RutaProy" with th path you save the files.

# Bibliography

[1] International Technical Standard E.164. Link: https://voipstudio.com/es/blog/que-es-el-formato-e-164-y-como-usarlo-correctamente/


