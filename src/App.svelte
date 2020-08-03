<script>
	import { fade } from 'svelte/transition';
	import "spectre.css/src/spectre.scss";
    import "spectre.css/src/spectre-icons.scss";
	import "spectre.css/src/spectre-exp.scss";
	
	var isLoading = false;
	async function submitHandler(e) {
		if (e)
			e.preventDefault();
		var email = document.getElementById("email");
		var error = document.getElementById("error");
		email.parentElement.classList.remove("has-error");
		var name = document.getElementById("name");
		name.parentElement.classList.remove("has-error");
		var comapny = document.getElementById("comapny");
		company.parentElement.classList.remove("has-error");
		var city = document.getElementById("city");
		city.parentElement.classList.remove("has-error");
		var state = document.getElementById("state");
		state.parentElement.classList.remove("has-error");
		var zip = document.getElementById("zip");
		zip.parentElement.classList.remove("has-error");
		var phone = document.getElementById("phone");
		if (!validateEmail(email.value)) {
			email.parentElement.classList.add("has-error");
			error.innerText = "Invalid email";
			return;
		}
		if (!name.value) {
			name.parentElement.classList.add("has-error");
			error.innerText = "Name is required";
			return;
		}
		if (!company.value) {
			company.parentElement.classList.add("has-error");
			error.innerText = "Company is required";
			return;
		}
		if (!city.value) {
			city.parentElement.classList.add("has-error");
			error.innerText = "City is required";
			return;
		}
		if (!state.value) {
			state.parentElement.classList.add("has-error");
			error.innerText = "State is required";
			return;
		}
		if (!zip.value) {
			zip.parentElement.classList.add("has-error");
			error.innerText = "Zip code is required";
			return;
		}
		if (isLoading)
			return;
		isLoading = true;
		var button = document.getElementById("button");
		button.classList.add("loading");
		let [res, err]  = await request("https://geosync.cloud/register", {
			method: "POST",
			headers: {
				'Accept': 'application/json',
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({
				email: email.value,
				name: name.value,
				company: company.value,
				city: city.value,
				state: state.value,
				zip: zip.value,
				phone: phone.value
			})
		});
		button.classList.remove("loading");
		isLoading = false;
		if (err) {
			error.innerText = err;
			return;
		}

		document.getElementById("form").style.display = "none";
		document.getElementById("footer").style.display = "none";
		document.getElementById("instructions").innerText = "Thanks for your interest"
		document.getElementById("message").innerText = "Activation information will be sent to the email address provided within one business day."
	}
	function validateEmail(email) {
		var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
		return re.test(String(email).toLowerCase());
	}
	function asyncErrorHandler(promise) {
		return promise
			.then(data => ([data, undefined]))
			.catch(error => Promise.resolve([undefined, error]));
	}
	async function request(url, params) {
		const [res, err] = await asyncErrorHandler(fetch(url, params));
		if (err) {
			return [undefined, "Connection error"]
		}

		const responseObj = await res.json();
		if (responseObj.status == "error")
			return [undefined, responseObj.message];
		else if (!res.ok)
			return [undefined, responseObj.message];

		return [responseObj, undefined]
	}
</script>

<main>
	<div id="header">
		<span class="helper"></span>
		<img class="logo" src="/seiler.png" alt="Logo"/>
	</div>
	<div class="panel" in:fade>
		<div class="panel-header">
			<h2><img class="logo" src="/ztools.png" alt="Z-Tools" /> GeoSync Z-Tools</h2>
			<span id="message">Easy to use toolbox for working with point clouds and photos.</span>
			<h5 id="instructions">Register to get started for free.</h5>
		</div>
		<div id="form" class="panel-body">
			<form onsubmit="submitHandler(event)" autocomplete="off">
				<div class="form-group">
					<label class="form-label" for="email">Email*</label>
					<input class="form-input" type="text" id="email" autofocus>
				</div>
				<div class="form-group">
					<label class="form-label" for="name">Name*</label>
					<input class="form-input" type="text" id="name">
				</div>
				<div class="form-group">
					<label class="form-label" for="company">Company*</label>
					<input class="form-input" type="text" id="company">
				</div>
				<div class="form-group">
					<label class="form-label" for="city">City*</label>
					<input class="form-input" type="text" id="city">
				</div>
				<div class="form-group">
					<label class="form-label" for="state">State*</label>
					<input class="form-input" type="text" id="state">
				</div>
				<div class="form-group">
					<label class="form-label" for="zip">Zip Code*</label>
					<input class="form-input" type="text" id="zip">
				</div>
				<div class="form-group">
					<label class="form-label" for="phone">Phone</label>
					<input class="form-input" type="tel" id="phone">
				</div>
				<input type="submit" style="position: absolute; left: -9999px"/>
			</form>
		</div>
		<div id="footer" class="panel-footer">
			<div class="tile tile-centered text-left">
				<div class="tile-icon">
					<button id="button" class="btn btn-primary mr-2" on:click={submitHandler}>Submit</button>
				</div>
				<div class="tile-content">
					<div id="error" class="tile-title error"></div>
				</div>
			</div>
		</div>
	</div>
</main>

<style>
	#header {
		text-align: center;
		width: 100%;
		height: 123px;
		background-color: #013893;
	}
	.helper {
		display: inline-block;
		height: 100%;
		vertical-align: middle;
	}
	.logo {
		vertical-align: middle;
	}
	.error {
		color: #e85600;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
		float: right;
	}
</style>