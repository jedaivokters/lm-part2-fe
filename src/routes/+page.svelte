<script>
	import { onMount } from 'svelte';

	let employees = [];
	let formFirstName = '';
	let formMiddleName = '';
	let formLastName = '';

	const allEmployees = async () => {
		let res = await fetch('http://localhost:8080/graphql', {
			method: 'POST',

			headers: {
				"Content-Type": "application/json"
			},

			body: JSON.stringify({
				query: `{
					findAllEmployees {
						id
						fullName
					}
				}`
			})
		})

		return await res.json()
	}

	const handleSubmit = async e => {
		let res = await fetch('http://localhost:8080/graphql', {
			method: 'POST',
			

			headers: {
				"Content-Type": "application/json"
			},

			body: JSON.stringify({
				query: `mutation {
					createEmployee(firstName: "${formFirstName}", middleName: "${formMiddleName}", lastName: "${formLastName}") {
						id
						firstName
					}
				}`
			})
		})

		window.$('#exampleModalCenter').modal('hide');
		window.$("#addSuccess").show();

		setTimeout(() => {
			//hide at 5secs
			window.$("#addSuccess").hide();
		}, 3000)

		return await res.json();
		
	}

	var loadEmployees = async () => {
		employees = await allEmployees();
		
		if (employees.data == null) {
			employees = [];
		} else {
			employees = employees.data.findAllEmployees;
		}
	}

	onMount(async () => {
		await loadEmployees();

		window.$('#exampleModalCenter').on('hidden.bs.modal	', async function () {
			await loadEmployees();

			formFirstName = '';
			formLastName = '';
			formMiddleName = '';
		});
	});

</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Test" />
</svelte:head>

<section>
	<div class = "d-flex" style="width: 100%;justify-content: end;">
		<button type="button" class="btn btn-primary" style="height: 50px;" data-toggle="modal" data-target="#exampleModalCenter">Add New Employee</button>
	</div>
	<div id="addSuccess" class="alert alert-success" style="display: none;width: 100%;" role="alert">
		Added new entry
	  </div>
	<table class="table table-striped">
		<thead>
		  <tr>
			<th scope="col">#</th>
			<th scope="col">Fullname</th>
			<th scope="col">Primary Address</th>
			<th scope="col">Primary Contact Info</th>
			<th scope="col">Age</th>
			<th scope="col"># Years in the company</th>
			<th scope="col">--</th>
		  </tr>
		</thead>
		<tbody>
			{#each employees as emp}
			<tr>
				<th scope="row">{ emp.id }</th>
				<td>{ emp.fullName }</td>
				<td>???</td>
				<td>???</td>
				<td>???</td>
				<td>???</td>
				<td>???</td>
			</tr>
			{/each}
		</tbody>
	  </table>

</section>

<!-- Modal -->
<div class="modal fade" id="exampleModalCenter" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
	<div class="modal-dialog modal-dialog-centered" role="document">
	  <div class="modal-content">
		<div class="modal-header">
		  <h5 class="modal-title" id="exampleModalLongTitle">New Employee</h5>
		  <button type="button" class="close" data-dismiss="modal" aria-label="Close">
			<span aria-hidden="true">&times;</span>
		  </button>
		</div>
		<form on:submit|preventDefault={handleSubmit}>
			<div class="modal-body">		
					<div class="form-group">
						<label for="firstName">First Name</label>
						<input type="text" bind:value="{ formFirstName }" required class="form-control" id="firstName" placeholder="First Name">
					</div>
					<div class="form-group">
						<label for="middleName">Middle Name</label>
						<input type="text" bind:value="{ formMiddleName }" class="form-control" id="middleName" placeholder="Middle Name">
					</div>
					<div class="form-group">
						<label for="lastName">Last Name</label>
						<input type="text" required bind:value="{ formLastName }" class="form-control" id="lastName" placeholder="Last Name">
					</div>
			</div>
			<div class="modal-footer">
			<button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>
			<button type="submit" class="btn btn-primary">Save changes</button>
			</div>
		</form>
	  </div>
	</div>
  </div>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}
</style>
