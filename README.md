# Django_Checkbook
A mock banking website with basic functionality to create a user, add transactions, and view the account balance. Made with Django.

![screenshotCheckbook](https://user-images.githubusercontent.com/84836870/134848137-7408a256-a761-4e48-82b0-a64f35247f12.png)

From models.py:
```python
class Transaction(models.Model):
    date = models.DateField()
    type = models.CharField(max_length=10, choices=TransactionTypes)
    amount = models.DecimalField(max_digits=15, decimal_places=2)
    description = models.CharField(max_length=100)
    account = models.ForeignKey(Account, on_delete=models.CASCADE)

    Transactions = models.Manager()
```

From AddTransaction.html:
```python
{% extends 'checkbook_base.html' %}
	{% block title%}Add New Transaction{% endblock %}
	{% block content %}
			<div class="new-transaction-form">
				<form method="POST">
					{% csrf_token %}
					{{ form.as_p }}
					<button id="new-transaction-button" type="submit" name="Save_Transaction">Add Transaction</button>
				</form>
				<br>	
			</div>
	{% endblock %}
```

From views.py:
```python
def balance(request, pk):
    account = get_object_or_404(Account, pk=pk)
    transactions = Transaction.Transactions.filter(account = pk)
    current_total = account.initial_deposit
    table_contents = { }
    for t in transactions:
        if t.type == 'Deposit':
            current_total += t.amount
            table_contents.update({t: current_total})
        else:
            current_total -= t.amount
            table_contents.update({t: current_total})
    content = {'account': account, 'table_contents': table_contents, 'balance': current_total}
    return render(request, 'checkbook/BalanceSheet.html', content)
```
