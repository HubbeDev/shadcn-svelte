<script lang="ts">
	import {
		createRender,
		createTable,
		Render,
		Subscribe
	} from "svelte-headless-table";
	import {
		addPagination,
		addSelectedRows,
		addSortBy,
		addTableFilter
	} from "svelte-headless-table/plugins";
	import {
		ChevronRight,
		ChevronLeft,
		DoubleArrowRight,
		DoubleArrowLeft
	} from "radix-icons-svelte";
	import { readable } from "svelte/store";
	import Actions from "./tasks-table-actions.svelte";
	import TasksCheckbox from "./tasks-table-checkbox.svelte";
	import * as Table from "@/registry/new-york/ui/table";
	import { Button } from "@/registry/default/ui/button";
	import * as Select from "@/registry/default/ui/select";

	import type { Task } from "../(data)/tasks";

	export let tasks: Task[];

	const table = createTable(readable(tasks), {
		sort: addSortBy(),
		filter: addTableFilter(),
		page: addPagination(),
		select: addSelectedRows()
	});

	const columns = table.createColumns([
		table.column({
			header: (_, { pluginStates }) => {
				const { allPageRowsSelected } = pluginStates.select;
				return createRender(TasksCheckbox, {
					checked: allPageRowsSelected
				});
			},
			accessor: ({ id }) => id,
			cell: ({ row }, { pluginStates }) => {
				const { getRowState } = pluginStates.select;
				const { isSelected } = getRowState(row);

				return createRender(TasksCheckbox, {
					checked: isSelected
				});
			},
			plugins: {
				sort: {
					disable: true
				},
				filter: {
					exclude: true
				}
			}
		}),
		table.column({
			accessor: "id",
			header: "Task"
		}),
		table.column({
			accessor: "title",
			header: "Title"
		}),
		table.column({
			accessor: "status",
			header: "Status"
		}),
		table.column({
			accessor: "priority",
			header: "Priority"
		}),
		table.column({
			header: "",
			accessor: ({ id }) => id,
			cell: (item) => {
				return createRender(Actions, { id: item.value });
			},
			plugins: {
				sort: {
					disable: true
				}
			}
		})
	]);
	const {
		headerRows,
		pageRows,
		tableAttrs,
		tableBodyAttrs,
		pluginStates,
		rows
	} = table.createViewModel(columns);

	const { hasNextPage, hasPreviousPage, pageIndex, pageCount, pageSize } =
		pluginStates.page;
	const { selectedDataIds } = pluginStates.select;

	let defaultPageSize = { value: 10, label: "10" };
</script>

<div class="w-full">
	<div class="space-y-4">
		<div class="flex items-center py-4" />
		<div class="rounded-md border">
			<Table.Root {...$tableAttrs}>
				<Table.Header>
					{#each $headerRows as headerRow}
						<Subscribe rowAttrs={headerRow.attrs()}>
							<Table.Row>
								{#each headerRow.cells as cell (cell.id)}
									<Subscribe
										attrs={cell.attrs()}
										let:attrs
										props={cell.props()}
									>
										<Table.Head {...attrs}>
											<Render of={cell.render()} />
										</Table.Head>
									</Subscribe>
								{/each}
							</Table.Row>
						</Subscribe>
					{/each}
				</Table.Header>
				<Table.Body {...$tableBodyAttrs}>
					{#each $pageRows as row (row.id)}
						<Subscribe rowAttrs={row.attrs()} let:rowAttrs>
							<Table.Row {...rowAttrs}>
								{#each row.cells as cell (cell.id)}
									<Subscribe attrs={cell.attrs()} let:attrs>
										<Table.Cell {...attrs}>
											<Render of={cell.render()} />
										</Table.Cell>
									</Subscribe>
								{/each}
							</Table.Row>
						</Subscribe>
					{/each}
				</Table.Body>
			</Table.Root>
		</div>
		<div class="flex items-center justify-between px-2">
			<div class="flex-1 text-sm text-muted-foreground">
				{Object.keys($selectedDataIds).length} of{" "}
				{$rows.length} row(s) selected.
			</div>
			<div class="flex items-center space-x-6 lg:space-x-8">
				<div class="flex items-center space-x-2">
					<p class="text-sm font-medium">Rows per page</p>
					<Select.Root
						onSelectedChange={/** Types need checkes */
						(selected) => {
							$pageSize = selected.value;
						}}
						selected={defaultPageSize}
					>
						<Select.Trigger class="w-[180px]">
							<Select.Value placeholder="Select page size" />
						</Select.Trigger>
						<Select.Content>
							<Select.Item value="10">10</Select.Item>
							<Select.Item value="20">20</Select.Item>
							<Select.Item value="30">30</Select.Item>
							<Select.Item value="40">40</Select.Item>
							<Select.Item value="50">50</Select.Item>
						</Select.Content>
					</Select.Root>
				</div>
				<div
					class="flex w-[100px] items-center justify-center text-sm font-medium"
				>
					Page {$pageIndex + 1} of {$pageCount}
				</div>
				<div class="flex items-center space-x-2">
					<Button
						variant="outline"
						class="hidden h-8 w-8 p-0 lg:flex"
						on:click={() => ($pageIndex = 0)}
						disabled={!$hasPreviousPage}
					>
						<span class="sr-only">Go to first page</span>
						<DoubleArrowLeft size={15} />
					</Button>
					<Button
						variant="outline"
						class="p-0 w-8 h-8"
						on:click={() => ($pageIndex = $pageIndex - 1)}
						disabled={!$hasPreviousPage}
					>
						<span class="sr-only">Go to previous page</span>
						<ChevronLeft size={15} />
					</Button>
					<Button
						variant="outline"
						class="p-0 w-8 h-8"
						disabled={!$hasNextPage}
						on:click={() => ($pageIndex = $pageIndex + 1)}
					>
						<span class="sr-only">Go to next page</span>
						<ChevronRight size={15} />
					</Button>
					<Button
						variant="outline"
						class="hidden h-8 w-8 p-0 lg:flex"
						disabled={!$hasNextPage}
						on:click={() =>
							($pageIndex =
								Math.ceil($rows.length / $pageRows.length) - 1)}
					>
						<span class="sr-only">Go to last page</span>
						<DoubleArrowRight size={15} />
					</Button>
				</div>
			</div>
		</div>
	</div>
</div>
