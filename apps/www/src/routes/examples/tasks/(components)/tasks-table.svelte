<script lang="ts">
	import {
		createRender,
		createTable,
		Render,
		Subscribe
	} from "svelte-headless-table";
	import { readable } from "svelte/store";
	import * as Table from "@/registry/new-york/ui/table";
	import Actions from "./tasks-table-actions.svelte";
	import type { Task } from "../(data)/tasks";

	export let tasks: Task[];

	const table = createTable(readable(tasks));

	const columns = table.createColumns([
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
	const { headerRows, pageRows, tableAttrs, tableBodyAttrs } =
		table.createViewModel(columns);
</script>

<div class="w-full">
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
	<div class="flex items-center justify-end space-x-2 py-4" />
</div>
