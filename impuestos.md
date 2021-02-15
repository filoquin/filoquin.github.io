# Calcular los impuestos de manera reversa

Imaginemos un escenario donde nos piden que mediante un wizard o desde otro modelo. Agreguemos un producto con un valor exacto en una orden de venta o presupuesto.

Cuando configuramos los impuestos en odoo como *"incluidos en el precio"*, lo podemos hacer de manera sencilla.
```python
                    rec.sale_order_id.order_line = [(0, 0, {
                        'order_id': rec.sale_order_id.id,
                        'product_id': rec.product_id.id,
                        'product_uom_qty': 1,
                        'price_unit': rec.amount,
                        'tax_id': [(6, 0, rec.product_id.taxes_id.ids)]
                    })]
```
Y listo

## Pero...

Si en nuestra configuración los **impuestos no estan incluidos en el precio**.
Si agrego un producto de $100 me agregaria al total $120

Antes de agregar el producto tenemos que descontarle los impuestos.
Si el valor es $100 el costo sin impuestos es aproximadamente $82,65.

Por suerte algun programador penso en nosotros y dejo un flag en la función **compute_all** de **account.tax** para que resolvamos este problema.
Si en el contexto agregamos **force_price_include=True** computa el precio con los impuestos incluidos. y asi obtendremos el valor sin impuestos de nuestro monto.

```python
                    tax_amount = rec.product_id.taxes_id.with_context(force_price_include=True).compute_all(rec.amount)
                    rec.sale_order_id.order_line = [(0, 0, {
                        'order_id': rec.sale_order_id.id,
                        'product_id': rec.product_id.id,
                        'product_uom_qty': 1,
                        'price_unit': tax_amount['total_excluded'],
                        'tax_id': [(6, 0, rec.product_id.taxes_id.ids)]
                    })]
```
Todavía podemos tener problemas de redondeo, pero ya estamos cerca de la solución.


