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
	let order = '';

	async function fetchUserData() {
		isLoading = true;
		try {
			const res = await dataAPI.get(
				`user?page=${page}&limit=${limit}&sortby=${sortby}&order=ASC&name=${querySearch}`
			);
			data = res.data.data;
			pagination = res.data.pagination;
		} catch (error) {
			data = [];
			console.log(error);
		} finally {
			// setTimeout(() => {
			// 	isLoading = false;
			// }, 2000);
			isLoading = false;
		}
	}

	onMount(() => {
		fetchUserData('');
	});
</script>

<div class=" bg-blue-300 h-screen w-screen flex items-center justify-center md:p-10 p-3">
	<div class="bg-blue-100 p-4 rounded-md w-full h-full flex flex-col gap-4">
		<span class="text-black text-2xl font-bold">Candidate List</span>
		<div class="bg-white p-4 rounded-md w-full h-full flex flex-col gap-4">
			{#if isLoading}
				<div class="flex justify-center items-center m-auto">
					<div
						class="animate-spin rounded-full h-32 w-32 border-t-2 border-b-2 border-blue-500"
					></div>
				</div>
			{:else}
				<div class="flex justify-between items-center">
					<div class="">
						<button class="btn bg-blue-500 text-white border-0">
							<Icon icon="akar-icons:plus" color="#fff" width="15" />
							<span class="ml-2">Add Candidate</span>
						</button>
					</div>
					<div class="flex gap-3">
						<div class="flex gap-3 items-center">
							<span class="text-black font-semibold md:block hidden">Sort:</span>
							<select
								class="select select-bordered w-fit max-w-md bg-white text-black border-blue-500 rounded-md focus:border-blue-500 focus:bg-blue-100 focus:text-black"
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
						<div class="flex gap-2">
							<form action="" class="w-full" on:submit={fetchUserData}>
								<input
									type="text"
									placeholder="Search . . ."
									class="self-end input w-full max-w-xs bg-white text-black border-blue-500 rounded-md focus:border-blue-500 focus:bg-blue-100 focus:text-black"
									bind:value={querySearch}
								/>
							</form>
							<button
								on:click={() => {
									querySearch = '';
									sortby = 'created_at';
									fetchUserData();
								}}
							>
								<Icon
									icon="akar-icons:cross"
									color={`${querySearch === '' ? 'transparent' : 'red'}`}
									width="20"
								/>
							</button>
						</div>
					</div>
				</div>
				<table class="table table-sm lg:table-lg text-black overflow-y-auto">
					<thead class="text-black">
						<tr class="bg-blue-300">
							<th>Id</th>
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
						{#if data.length === 0}
							<tr>
								<td colspan="10" class="text-center">No Data</td>
							</tr>
						{:else}
							{#each data as user}
								<tr>
									<td>{user.candidate_id}</td>
									<td class="md:table-cell hidden">{user.email}</td>
									<td class="md:table-cell hidden">{user.phone_number}</td>
									<td>{user.full_name}</td>
									<td class="xl:table-cell hidden">{user.dob}</td>
									<td class="xl:table-cell hidden">{user.pob}</td>
									<td class="xl:table-cell hidden">{user.gender}</td>
									<td class="2xl:table-cell hidden">{user.year_exp}</td>
									<td class="2xl:table-cell hidden">{user.last_salary}</td>
									<td class="flex gap-2">
										<button
											class="btn btn-sm border-0 bg-blue-500"
											onclick="detail_modal.showModal()"
										>
											<Icon icon="solar:eye-broken" color="#fff" width="15" />
										</button>
										<button class="btn btn-sm border-0 bg-red-500">
											<Icon icon="uil:trash-alt" color="#fff" width="15" />
										</button>
									</td>
								</tr>
							{/each}
						{/if}
					</tbody>
				</table>
			{/if}
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
					<button class="btn btn-sm border-0 bg-blue-500">
						<Icon icon="akar-icons:arrow-left" color="#fff" width="15" />
					</button>
				{/if}

				{#if page < pagination.totalPage}
					<span class="text-black">
						{page * limit - (limit - 1)} - {page * limit} report from
						{pagination.totalData} reports
					</span>
				{:else}
					<span class="text-black">
						{page * limit - (limit - 1)} - {pagination.totalData} report from
						{pagination.totalData} reports
					</span>
				{/if}

				{#if page < pagination.totalPage}
					<button class="btn btn-sm border-0 bg-blue-500">
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
<dialog id="detail_modal" class="modal">
	<div class="modal-box">
		<h3 class="font-bold text-lg">Hello!</h3>
		<p class="py-4">Press ESC key or click outside to close</p>
	</div>
	<form method="dialog" class="modal-backdrop">
		<button>close</button>
	</form>
</dialog>
