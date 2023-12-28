<script>
	import { onMount } from 'svelte';
	import dataAPI from '../utils/axios';
	import Icon from '@iconify/svelte';

	let data = [];
	let pagination = {};
	let isLoading = false;

	let page = 1;
	let limit = 10;
	let querySearch = '';
	let sortby = '' || 'created_at';
	let order = 'ASC';

	let userId = '';
	let userDetail = {};

	let u_fullName = '';
	let u_email = '';
	let u_phoneNumber = '';
	let u_pob = '';
	let u_dob = '';
	let u_lastSalary = '';
	let u_yearExp = '';
	let u_gender = '';

	let c_fullName = '';
	let c_email = '';
	let c_phoneNumber = '';
	let c_pob = '';
	let c_dob = '';
	let c_lastSalary = '';
	let c_yearExp = '';
	let c_gender = '';

	async function fetchUserData() {
		isLoading = true;
		try {
			const res = await dataAPI.get(
				`user?page=${page}&limit=${limit}&sortby=${sortby}&order=${order}&name=${querySearch}`
			);
			data = res.data.data;
			pagination = res.data.pagination;
		} catch (error) {
			data = [];
			console.log(error);
		} finally {
			// setTimeout(() => {
			// 	isLoading = false;
			// }, 20000);
			isLoading = false;
		}
	}

	async function fetchUserDetail() {
		try {
			const res = await dataAPI.get(`user/${userId}`);
			userDetail = res.data.data[0];
		} catch (error) {
			userDetail = {};
			console.log(error);
		} finally {
		}
	}

	async function createUser(e) {
		try {
			e.preventDefault();

			await dataAPI
				.post(`user/create`, {
					full_name: c_fullName,
					email: c_email,
					phone_number: c_phoneNumber,
					pob: c_pob,
					dob: c_dob,
					gender: c_gender,
					last_salary: c_lastSalary,
					year_exp: c_yearExp
				})
				.then((res) => {
					c_fullName = '';
					c_email = '';
					c_phoneNumber = '';
					c_pob = '';
					c_dob = '';
					c_gender = '';
					c_lastSalary = '';
					c_yearExp = '';
				});

			alert('Create Success');
			window.location.reload();
		} catch (error) {
			console.log(error);
		} finally {
		}
	}

	async function updateUser(e) {
		try {
			e.preventDefault();

			await dataAPI
				.patch(`user/update/${userId}`, {
					full_name: u_fullName,
					email: u_email,
					phone_number: u_phoneNumber,
					pob: u_pob,
					dob: u_dob,
					gender: u_gender,
					last_salary: u_lastSalary,
					year_exp: u_yearExp
				})
				.then((res) => {
					u_fullName = '';
					u_email = '';
					u_phoneNumber = '';
					u_pob = '';
					u_dob = '';
					u_gender = '';
					u_lastSalary = '';
					u_yearExp = '';
				});

			alert('Update Success');
			await fetchUserData();
			document.getElementById('update_user').close();
		} catch (error) {
			console.log(error);
		} finally {
		}
	}

	async function deleteUser() {
		try {
			await dataAPI.delete(`user/delete/${userId}`);
			alert('Delete Success');
			window.location.reload();
		} catch (error) {
			console.log(error);
		} finally {
		}
	}

	function formatCurrency(number) {
		if (isNaN(number)) {
			return 'Invalid number';
		}
		let parts = number.toString().split('.');
		parts[0] = parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ',');
		return parts.join('.');
	}

	function handleNextPage() {
		page += 1;
		fetchUserData();
	}

	function handlePrevPage() {
		page -= 1;
		fetchUserData();
	}

	onMount(() => {
		fetchUserData();
	});
</script>

<div class=" bg-blue-300 h-screen w-screen flex items-center justify-center md:p-8 p-2">
	<div class="bg-blue-100 p-3 sm:p-4 rounded-md w-full h-full flex flex-col gap-4">
		<span class="text-black text-2xl font-bold">Candidate List</span>
		<div class="bg-white p-3 sm:p-4 rounded-md w-full h-full flex flex-col gap-4">
			<div class="flex justify-between items-center">
				<div class="">
					<button
						class="btn bg-blue-500 btn-sm md:btn-md text-white border-0 me-3"
						onclick="create_user.showModal()"
					>
						<Icon icon="akar-icons:plus" color="#fff" width="15" />
						<span class="ml-2 sm:block hidden">Add Candidate</span>
					</button>
				</div>
				<div class="flex gap-1 md:gap-3 items-center">
					<div class="flex gap-3 items-center">
						<span class="text-black font-semibold md:block hidden">Sort:</span>
						<select
							class="select select-bordered select-sm md:select-md w-fit text-xs md:text-md max-w-md bg-white text-black border-blue-500 rounded-md focus:border-blue-500 focus:bg-blue-100 focus:text-black"
							on:change={(e) => {
								sortby = e.target.value;
								fetchUserData();
							}}
							bind:value={sortby}
						>
							<option value="created_at" disabled selected>Created</option>
							<option value="email">Email</option>
							<option value="full_name">Full Name</option>
							<option value="gender">Gender </option>
							<option value="phone_number">Phone Number</option>
							<option value="dob">Date of Birth</option>
							<option value="year_exp">Year Experience</option>
							<option value="last_salary">Last Salary</option>
						</select>
					</div>
					{#if order === 'ASC'}
						<button
							class="btn border-0 bg-blue-500 btn-sm md:btn-md"
							on:click={() => {
								order = 'DESC';
								fetchUserData();
							}}
						>
							<Icon icon="akar-icons:arrow-down" color="#fff" width="15" />
						</button>
					{:else}
						<button
							class="btn border-0 bg-blue-500 btn-sm md:btn-md"
							on:click={() => {
								order = 'ASC';
								fetchUserData();
							}}
						>
							<Icon icon="akar-icons:arrow-up" color="#fff" width="15" />
						</button>
					{/if}
					<div class="flex relative">
						<form action="" class="w-full" on:submit={fetchUserData}>
							<input
								type="text"
								placeholder="Search . . ."
								class="self-end input input-sm md:input-md w-full max-w-xs bg-white text-black border-blue-500 rounded-md focus:border-blue-500 focus:bg-blue-100 focus:text-black"
								bind:value={querySearch}
							/>
						</form>
						<button
							class=" btn-sm md:btn-md absolute right-0"
							on:click={() => {
								querySearch = '';
								sortby = 'created_at';
								fetchUserData();
							}}
						>
							<Icon
								icon="akar-icons:cross"
								width="20"
								color={`${querySearch === '' ? 'transparent' : 'red'}`}
							/>
						</button>
					</div>
				</div>
			</div>
			<div class="overflow-y-auto h-full">
				<table class="table table-sm lg:table-md text-black">
					<thead class="text-black">
						<tr class="bg-blue-300 text-xs sm:text-sm">
							<th>Candidate ID</th>
							<th class="md:table-cell hidden">Email</th>
							<th class="md:table-cell hidden">Phone Number</th>
							<th>Full Name</th>
							<th class="xl:table-cell hidden">Date of Birth</th>
							<th class="xl:table-cell hidden">Place of Birth</th>
							<th class="xl:table-cell hidden">Gender</th>
							<th class="2xl:table-cell hidden">Year Experience</th>
							<th class="2xl:table-cell hidden">Last Salary</th>
							<th>Action</th>
						</tr>
					</thead>
					<tbody>
						{#if isLoading}
							<tr>
								<td colspan="10">
									<div class="flex justify-center items-center m-auto">
										<Icon icon="eos-icons:three-dots-loading" color="#000" width="50" />
									</div>
								</td>
							</tr>
						{:else if data.length === 0}
							<tr>
								<td colspan="10" class="text-center">No Data</td>
							</tr>
						{:else}
							{#each data as user}
								<tr>
									<td class="text-xs sm:text-sm">{user.candidate_id}</td>
									<td class="md:table-cell hidden">{user.email}</td>
									<td class="md:table-cell hidden">{user.phone_number}</td>
									<td class="text-xs sm:text-sm">{user.full_name}</td>
									<td class="xl:table-cell hidden">{user.dob}</td>
									<td class="xl:table-cell hidden">{user.pob}</td>
									<td class="xl:table-cell hidden">{user.gender}</td>
									<td class="2xl:table-cell hidden"
										>{user.year_exp} {user.year_exp > 1 ? 'Years' : 'Year'}</td
									>
									<td class="2xl:table-cell hidden">{formatCurrency(user.last_salary)} </td>
									<td class="flex gap-1">
										<button
											class="btn btn-xs sm:btn-sm border-0 bg-blue-500"
											onclick="detail_user.showModal()"
											on:click={() => {
												userId = user.candidate_id;
												fetchUserDetail();
											}}
										>
											<Icon icon="solar:eye-broken" color="#fff" width="15" />
										</button>
										<button
											class="btn btn-xs sm:btn-sm border-0 bg-green-500"
											onclick="update_user.showModal()"
											on:click={() => {
												userId = user.candidate_id;
												fetchUserDetail();
											}}
										>
											<Icon icon="akar-icons:edit" color="#fff" width="15" />
										</button>
										<button
											class="btn btn-xs sm:btn-sm border-0 bg-red-500"
											onclick="delete_user.showModal()"
											on:click={() => {
												userId = user.candidate_id;
											}}
										>
											<Icon icon="uil:trash-alt" color="#fff" width="15" />
										</button>
									</td>
								</tr>
							{/each}
						{/if}
					</tbody>
				</table>
			</div>
		</div>
		{#if isLoading}
			<div class="flex justify-center items-center m-auto">
				<Icon icon="eos-icons:three-dots-loading" color="#000" width="50" />
			</div>
		{:else}
			<div class="flex justify-center items-center gap-2">
				{#if page === 1}
					<button class="btn btn-sm border-0 bg-blue-500" disabled>
						<Icon icon="akar-icons:arrow-left" color="#fff" width="15" />
					</button>
				{:else}
					<button class="btn btn-sm border-0 bg-blue-500" on:click={handlePrevPage}>
						<Icon icon="akar-icons:arrow-left" color="#fff" width="15" />
					</button>
				{/if}

				{#if page < pagination.totalPage}
					<span class="text-black sm:text-base text-xs">
						{page * limit - (limit - 1)} - {page * limit} candidate from
						{pagination.totalData} candidates
					</span>
				{:else}
					<span class="text-black sm:text-base text-xs">
						{page * limit - (limit - 1)} - {pagination.totalData} candidate from
						{pagination.totalData} candidates
					</span>
				{/if}

				{#if page < pagination.totalPage}
					<button class="btn btn-sm border-0 bg-blue-500" on:click={handleNextPage}>
						<Icon icon="akar-icons:arrow-right" color="#fff" width="15" />
					</button>
				{:else}
					<button class="btn btn-sm border-0 bg-blue-500" disabled>
						<Icon icon="akar-icons:arrow-right" color="#fff" width="15" />
					</button>
				{/if}
			</div>
		{/if}
	</div>
</div>

<!-- Modals -->
<dialog id="detail_user" class="modal">
	<div class="modal-box bg-blue-100 text-black">
		<h3 class="text-lg">
			User Detail
			<span class="font-bold">{userDetail.full_name}</span>
		</h3>
		<div class="flex flex-col gap-2 mt-3">
			<div class="flex flex-col gap-1 w-full p-3 overflow-y-auto bg-white rounded-md">
				<span class="text-xs sm:text-sm font-semibold">Candidate ID:</span>
				<span class="text-sm sm:text-sm">
					{userDetail.candidate_id}
				</span>
			</div>
			<div class="flex w-full p-4 overflow-y-auto bg-white rounded-md">
				<div class="flex flex-col gap-1 w-1/2">
					<span class="text-xs sm:text-sm font-semibold">Email:</span>
					<span class="text-sm sm:text-sm">
						{userDetail.email}
					</span>
				</div>
				<div class="flex flex-col gap-1 w-1/2">
					<span class="text-xs sm:text-sm font-semibold">Phone Number:</span>
					<span class="text-sm sm:text-sm">
						{userDetail.phone_number}
					</span>
				</div>
			</div>
			<div class="flex w-full p-4 overflow-y-auto bg-white rounded-md">
				<div class="flex flex-col gap-1 w-1/2">
					<span class="text-xs sm:text-sm font-semibold">Place, Date of Birth:</span>
					<span class="text-sm sm:text-sm">
						{userDetail.pob}, {userDetail.dob}
					</span>
				</div>
				<div class="flex flex-col gap-1 w-1/2">
					<span class="text-xs sm:text-sm font-semibold">Gender</span>
					<span class="text-sm sm:text-sm">
						{userDetail.gender}
					</span>
				</div>
			</div>
			<div class="flex w-full p-4 overflow-y-auto bg-white rounded-md">
				<div class="flex flex-col gap-1 w-1/2">
					<span class="text-xs sm:text-sm font-semibold">Year of Experience</span>
					<span class="text-sm sm:text-sm">
						{userDetail.year_exp}
					</span>
				</div>
				<div class="flex flex-col gap-1 w-1/2">
					<span class="text-xs sm:text-sm font-semibold">Last Salary</span>
					<span class="text-sm sm:text-sm">
						{formatCurrency(userDetail.last_salary)}
					</span>
				</div>
			</div>
		</div>
	</div>
	<form method="dialog" class="modal-backdrop">
		<button>close</button>
	</form>
</dialog>

<dialog id="update_user" class="modal">
	<div class="modal-box bg-blue-100 text-black">
		<h3 class="text-lg">
			Update Candidate Id:
			<span class="font-bold">{userDetail.candidate_id}</span>
		</h3>
		<div class="flex flex-col gap-2 mt-3">
			<div class="flex flex-col gap-1 w-full p-3 overflow-y-auto bg-white rounded-md">
				<span class="text-xs sm:text-sm font-semibold">Full Name:</span>
				<input
					type="text"
					placeholder={userDetail.full_name}
					class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
					bind:value={u_fullName}
				/>
			</div>
			<div class="flex flex-col gap-4 w-full p-4 overflow-y-auto bg-white rounded-md">
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold">Email:</span>
					<input
						type="text"
						placeholder={userDetail.email}
						class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
						bind:value={u_email}
					/>
				</div>
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold">Phone Number:</span>
					<input
						type="text"
						placeholder={userDetail.phone_number}
						class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
						bind:value={u_phoneNumber}
					/>
				</div>
			</div>
			<div class="flex flex-col gap-4 w-full p-4 overflow-y-auto bg-white rounded-md">
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold">Place, Date of Birth:</span>
					<div class="flex gap-1">
						<input
							type="text"
							placeholder={userDetail.pob}
							class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
							bind:value={u_pob}
						/>
						<input
							type="date"
							placeholder={userDetail.dob}
							class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
							bind:value={u_dob}
						/>
					</div>
				</div>
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold">Gender</span>
					<div class="flex items-center gap-10">
						<div class="flex items-center gap-2">
							<input
								type="radio"
								name="gender"
								id="Male"
								value="Male"
								class="radio radio-primary"
								bind:group={u_gender}
								checked={userDetail.gender === 'Male'}
							/>
							<label for="Male">
								<span>Male</span>
							</label>
						</div>

						<div class="flex items-center gap-2">
							<input
								type="radio"
								name="gender"
								id="Female"
								value="Female"
								class="radio radio-primary"
								bind:group={u_gender}
								checked={userDetail.gender === 'Female'}
							/>
							<label for="Female">
								<span>Female</span>
							</label>
						</div>
					</div>
				</div>
			</div>
			<div class="flex flex-col w-full p-4 overflow-y-auto bg-white rounded-md">
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold"
						>Years of Experience

						{#if u_yearExp < 0}
							<span class="text-xs text-red-500 font-normal italic"
								>Year of Experience must be greater than 0</span
							>
						{:else}
							<span class="text-xs text-transparent select-none">-</span>
						{/if}
					</span>
					<input
						type="number"
						placeholder={userDetail.year_exp}
						class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
						min="0"
						bind:value={u_yearExp}
					/>
				</div>
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold"
						>Last Salary
						{#if u_lastSalary < 0}
							<span class="text-xs text-red-500 font-normal italic"
								>Last Salary must be greater than 0</span
							>
						{:else}
							<span class="text-xs text-transparent select-none">-</span>
						{/if}
					</span>
					<input
						type="number"
						placeholder={userDetail.last_salary}
						class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
						min="0"
						bind:value={u_lastSalary}
					/>
				</div>
			</div>
		</div>
		<div class="modal-action">
			<form method="dialog">
				<!-- if there is a button in form, it will close the modal -->
				<button class="btn btn-error text-white">Cancel</button>
				{#if u_yearExp < 0 || u_lastSalary < 0}
					<button class="btn btn-primary text-white opacity-40 pointer-events-none">Update</button>
				{:else}
					<button class="btn btn-primary text-white" on:click={updateUser}>Update</button>
				{/if}
			</form>
		</div>
	</div>
</dialog>

<dialog id="create_user" class="modal">
	<div class="modal-box bg-blue-100 text-black">
		<h3 class="text-lg">Add New Candidate</h3>
		<div class="flex flex-col gap-2 mt-3">
			<div class="flex flex-col gap-1 w-full p-3 overflow-y-auto bg-white rounded-md">
				<span class="text-xs sm:text-sm font-semibold"
					>Full Name: <span class="text-red-500 text-xs">*</span></span
				>
				<input
					type="text"
					class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
					bind:value={c_fullName}
				/>
			</div>
			<div class="flex flex-col gap-4 w-full p-4 overflow-y-auto bg-white rounded-md">
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold"
						>Email: <span class="text-red-500 text-xs">*</span></span
					>
					<input
						type="text"
						class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
						bind:value={c_email}
					/>
				</div>
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold">Phone Number:</span>
					<input
						type="text"
						class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
						bind:value={c_phoneNumber}
					/>
				</div>
			</div>
			<div class="flex flex-col gap-4 w-full p-4 overflow-y-auto bg-white rounded-md">
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold"
						>Place, Date of Birth: <span class="text-red-500 text-xs">*</span></span
					>
					<div class="flex gap-1">
						<input
							type="text"
							class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
							bind:value={c_pob}
						/>
						<input
							type="date"
							class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
							bind:value={c_dob}
						/>
					</div>
				</div>
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold"
						>Gender <span class="text-red-500 text-xs">*</span></span
					>
					<div class="flex items-center gap-10">
						<div class="flex items-center gap-2">
							<input
								type="radio"
								name="gender"
								id="Male"
								value="Male"
								class="radio radio-primary"
								bind:group={c_gender}
							/>
							<label for="Male">
								<span>Male</span>
							</label>
						</div>

						<div class="flex items-center gap-2">
							<input
								type="radio"
								name="gender"
								id="Female"
								value="Female"
								class="radio radio-primary"
								bind:group={c_gender}
							/>
							<label for="Female">
								<span>Female</span>
							</label>
						</div>
					</div>
				</div>
			</div>
			<div class="flex flex-col w-full p-4 overflow-y-auto bg-white rounded-md">
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold"
						>Years of Experience <span class="text-red-500 text-xs">*</span>

						{#if c_yearExp < 0}
							<span class="text-xs text-red-500 font-normal italic"
								>Year of Experience must be greater than 0</span
							>
						{:else}
							<span class="text-xs text-transparent select-none">-</span>
						{/if}
					</span>
					<input
						type="number"
						class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
						min="0"
						bind:value={c_yearExp}
					/>
				</div>
				<div class="flex flex-col gap-1 w-full">
					<span class="text-xs sm:text-sm font-semibold"
						>Last Salary
						{#if c_lastSalary < 0}
							<span class="text-xs text-red-500 font-normal italic"
								>Last Salary must be greater than 0</span
							>
						{:else}
							<span class="text-xs text-transparent select-none">-</span>
						{/if}
					</span>
					<input
						type="number"
						class="input input-bordered input-sm w-full max-w-xs bg-white text-black rounded-md focus:bg-blue-100 focus:text-black placeholder:text-slate-700"
						min="0"
						bind:value={c_lastSalary}
					/>
				</div>

				<span class="text-red-500 text-xs italic mt-5">*) Required</span>
			</div>
		</div>
		<div class="modal-action">
			<form method="dialog">
				<!-- if there is a button in form, it will close the modal -->
				<button class="btn btn-error text-white">Cancel</button>
				{#if c_yearExp < 0 || c_lastSalary < 0 || c_fullName === '' || c_dob === '' || c_pob === '' || c_gender === '' || c_yearExp === ''}
					<button class="btn btn-primary text-white opacity-40 pointer-events-none">Add</button>
				{:else}
					<button class="btn btn-primary text-white" on:click={createUser}>Add</button>
				{/if}
			</form>
		</div>
	</div>
</dialog>

<dialog id="delete_user" class="modal">
	<div class="modal-box bg-red-100 text-black">
		<h3 class="font-bold text-lg">Delete User</h3>
		<p class="py-4">Are you sure you want to delete this user?</p>
		<div class="modal-action">
			<form method="dialog">
				<!-- if there is a button in form, it will close the modal -->
				<button class="btn btn-primary text-white">Cancel</button>
				<button class="btn btn-error text-white" on:click={deleteUser}>Delete</button>
			</form>
		</div>
	</div>
</dialog>
