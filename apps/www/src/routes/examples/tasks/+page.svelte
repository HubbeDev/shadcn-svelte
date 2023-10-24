<script lang="ts">
	import { TasksTable } from "./(components)";
	import { Button } from "@/registry/new-york/ui/button";
	import * as DropdownMenu from "@/registry/new-york//ui/dropdown-menu";
	import { tasks } from "./(data)/tasks";
</script>

<div class="md:hidden">
	<img
		src="/examples/playground-light.png"
		width={1280}
		height={916}
		alt="Playground"
		class="block dark:hidden"
	/>
	<img
		src="/examples/playground-dark.png"
		width={1280}
		height={916}
		alt="Playground"
		class="hidden dark:block"
	/>
</div>

<div class="hidden h-full flex-1 flex-col space-y-8 p-8 md:flex">
	<div class="flex items-center justify-between space-y-2">
		<div>
			<h2 class="text-2xl font-bold tracking-tight">Welcome back!</h2>
			<p class="text-muted-foreground">
				Here's a list of your tasks for this month!
			</p>
		</div>
		<div class="flex items-center space-x-2">
			<DropdownMenu.Root>
				<DropdownMenu.Trigger asChild let:builder>
					<Button
						class="  relative h-8 w-8 rounded-full"
						builders={[builder]}
						variant="outline"
					>
						<span
							class="relative flex shrink-0 overflow-hidden rounded-full h-9 w-9"
							><img
								class="aspect-square h-full w-full"
								alt="@shadcn"
								src="/avatars/03.png"
							/></span
						>
					</Button>
				</DropdownMenu.Trigger>
				<DropdownMenu.Content class="w-56">
					<DropdownMenu.Label>
						<p class="text-sm font-medium leading-none">
							shadcn-svelte
						</p>
						<p class="text-xs leading-none text-muted-foreground">
							m@example.com
						</p>
					</DropdownMenu.Label>
					<DropdownMenu.Separator />
					<DropdownMenu.Group>
						<DropdownMenu.Item>
							<span>Profile</span>
							<DropdownMenu.Shortcut>⇧⌘P</DropdownMenu.Shortcut>
						</DropdownMenu.Item>
						<DropdownMenu.Item>
							<span>Billing</span>
							<DropdownMenu.Shortcut>⌘B</DropdownMenu.Shortcut>
						</DropdownMenu.Item>
						<DropdownMenu.Item>
							<span>Settings</span>
							<DropdownMenu.Shortcut>⌘S</DropdownMenu.Shortcut>
						</DropdownMenu.Item>
						<DropdownMenu.Item>
							<span>New Team</span>
						</DropdownMenu.Item>
					</DropdownMenu.Group>
					<DropdownMenu.Separator />
					<DropdownMenu.Item>
						<span>Log out</span>
						<DropdownMenu.Shortcut>⇧⌘Q</DropdownMenu.Shortcut>
					</DropdownMenu.Item>
				</DropdownMenu.Content>
			</DropdownMenu.Root>
		</div>
	</div>
	<div class="space-y-4">
		<TasksTable {tasks} />
	</div>
</div>
