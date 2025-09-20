<template>
	<el-container class="wrapper">

		<el-header style="display: flex; align-items: center; justify-content: space-between;">
			<img src="@/images/中软颐养.png" height="60px" width="80px">
			<div style="margin-left: 500px;flex: 1;">
				<h1>颐养中心</h1>
			</div>
			<div style="flex: 1;">
				<el-button style="margin-left: 600px;background: white;color: #007BFF;border: 1px solid #007BFF;"
					@click="login">退出登录</el-button>
			</div>


		</el-header>

		<el-container>
			<el-aside width="200px">
				<ZzaMenu />
			</el-aside>
			<el-container>
				<el-main class="zhong">
					<router-view />
				</el-main>

			</el-container>
		</el-container>
	</el-container>
</template>

<script setup>
	import router from '@/router'
	import ZzaMenu from '@/components/menu';
	const login = () => {
		router.push("/");
	}
</script>

<style scoped lang="scss">
	$zzaborder: 1px solid #cccccc;

	.wrapper {
		height: 100vh;

		.el-header {
			border-bottom: 1px solid #007BFF;
			//background: linear-gradient(-90deg, #e9e3ca, #f2efe1);//和图片色一样，不能改
			background-color: #E6F2FF;
			color: #007BFF;
			border-bottom: $zzaborder;
			h1 {
				//line-height: 60px;
				font-size: 2.2rem;
				font-weight: bold;
				color: #007BFF;
				text-align: center;

			}
		}

		.el-aside {
			border-right: $zzaborder;
			//background: linear-gradient(-90deg, #e9e3ca, #f2efe1);
			background-color: #E6F2FF;
		}

		.el-main {
			border-bottom: $zzaborder;
		}

		.el-footer {
			h3 {
				line-height: 60px;
			}
		}
	}
</style>