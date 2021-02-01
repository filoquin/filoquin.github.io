# Acceder a los datos de remitos desde la factura

Algunas veces es necesario poder acceder a datos del remito desde la factura. Esto se puede solucionar con un campo computado

```Python3
class accountMove(models.Model):
    _inherit_id = 'account.move'

    picking_ids = fields.Many2many(
        'stock.picking',
        string='Pickings',
        compute='compute_picking_ids',
        search="search_picking_ids"
    )

    def compute_picking_ids(self):
        for move in self:
            sale_order_ids = self.env['sale.order'].search(
                [('invoice_ids', '=', move.id)])
            move.picking_ids = [(6, 0, sale_order_ids.picking_ids.ids)]

    def search_picking_ids(self, operator, value):
        invoice_ids = self.env['sale.order'].search(['picking_ids', operator, value]).mapped('invoice_ids')
        return [('id', 'in', invoice_ids.ids)]


```
