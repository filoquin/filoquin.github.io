# Preimpreso al puro QWEB
Muchas veces tenemos que imprimir desde odoo sobre formularios preimpresos. Si bien hay modulos que resuelven esto de manera elegante. Es posible que el cliente  solo requiera imprimirlos sin hacer ninguna traza.

Estos casos podemos resolverlos sin usar nuevos campo o logica programada. **Solo creando reportes qweb**.
En este ejemplo imprimiremos original y duplicado de un remito en una hoja A4.

## 1 creamos el formato del papel. 
Es una hoja A4 pero sin margenes.

	<record id="paperformat_preimpreso" model="report.paperformat">
			<field name="name">preimpreso</field>
			<field eval="False" name="default"/>
			<field name="format">A4</field>
			<field name="orientation">Portrait</field>
			<field name="margin_top">0</field>
			<field name="margin_bottom">0</field>
			<field name="margin_left">0</field>
			<field name="margin_right">0</field>
			<field eval="False" name="header_line"/>
			<field name="header_spacing">0</field>
			<field name="dpi">72</field>
	</record>

## 2 Creo la plantilla y corto la cantidad de lineas
La linea **t-foreach="[o.move_lines[i:i+15] for i in range(0,len(o.move_lines),15)]"**  divide las lineas del remito en grupos de 15. 
Los grupos de lineas estan dentro de la variable **chunk_lines**


	<template id="report_preimpreso">
			<t t-call="report.html_container">
				<t t-as="o" t-foreach="docs">
					<t t-as="chunk_lines" t-foreach="[o.move_lines[i:i+15] for i in range(0,len(o.move_lines),15)]">
						<t t-call="ba_warehouse.report_preimpreso_pages"/>
					</t>
				</t>
			</t>
	</template>
    

## 3 creo un template que contiene las dos copias de  remito
Solamente si necesito imprimir dos copias en la misma hoja

	<template id="report_preimpreso_pages">
			<div class="page" style="zoom:1.25;font-size:0.8em;">
				<div style="width:100%;height:148mm; position:absolute;">
					<t t-call="ba_warehouse.report_preimpreso_internal"/>
				</div>
				<div style="width:100%; top:140mm;height:148mm;position:absolute;">
					<t t-call="ba_warehouse.report_preimpreso_internal"/>
				</div>
			</div>
	</template>
 
 ## 4 Creo el diseño interno
 Cada elemento ubica un campo en una ubicacion en centimetros. Esto lo hacemos un poco a prueba y error porque **wkhtmltopdf** puede estar configurado a distintas resoluciones.
    
     <template id="report_preimpreso_internal">
			<span style="position:absolute; left:130mm;top:25mm" t-field="o.min_date"  t-field-options='{"format": "dd/mm/yyyy"}'/>
			<span style="position:absolute; left:152mm;top:25mm" t-field="o.name" />

			<span style="position:absolute; left:25mm;top:45mm" t-field="o.partner_id.display_name"/>
			<span style="position:absolute; left:150mm;top:45mm" t-field="o.partner_id.document_number"/>
			<span style="position:absolute; left:25mm;top:49mm" t-field="o.partner_id.street"/>
			<span style="position:absolute; left:150mm;top:49mm" t-field="o.partner_id.city_id.name"/>
			<div id="item_box" style="position:absolute; left:15mm;top:65mm">
				<t t-as="line" t-foreach="chunk_lines">
					<div>
						<div style="left:15mm;display:inline-block;width:45mm;" t-field="line.product_uom_qty"/>
						<div style="left:64mm;display:inline-block; width130mm; overflow:hidden;white-space: nowrap;" t-field="line.name"/>
					</div>
				</t>
			</div>
			<div style="width:160mm;position:absolute; left:25mm;top:130mm;height:10mm; overflow:hidden;" t-field="o.note"/>
		</template>
   
   ** 5 registro el reporte y le asigno el tamaño de pagina
   	
    <report id="ba_report_preimpreso" model="stock.picking" name="stock.report_preimpreso" report_type="qweb-pdf" string="Preimpreso"/>
		<record id="ba_report_preimpreso" model="ir.actions.report.xml">
			<field name="paperformat_id" ref="paperformat_preimpreso"/>
		</record>
   
   
   [Todo junto y en un solo archivo esta aca](https://gist.github.com/filoquin/d6f2c3e014a1269ec7bce555db561d71)
