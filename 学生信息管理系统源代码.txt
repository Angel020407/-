#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#include <conio.h>
#include <string.h>

int flag_type = -1;

struct User
{
	char num[100];
	char name[100];
	char password[100];
	char type[100];
	double achie;
	int arship;
	struct User* next;
};

void logon();							//登录
void encrytion(char*);					//密码
void exit();							//退出
void interface();						//界面
void xsxx_cz();							//查找
void xsxx_sc();							//删除
void xsxx_xg();							//修改
void xsxx_tj();							//添加
void xsxx_ll();							//录入

struct User* temp_storage(struct User* head);		//创建链表
int write_data_Student(struct User*);				//写入文件
int write_data_Student_two(struct User*);			//追加写入
void free_list_User(struct User*);					//释放空间

void logon()
{
	struct User lsu, flsu;
	printf("****欢迎使用学生信息管理系统****\n\n");
	system("pause");
	system("cls");
	printf("***登陆系统***\n\n");
	printf("请输入学号:\n");
	scanf("%s", lsu.num);
	printf("请输入密码:\n");
	encrytion(lsu.password);
	int flag = -1;
	int flag_t = -1;
	FILE* fpu;
	do
	{
		fpu = fopen("D://学生信息系统//用户信息.txt", "r");
		if (fpu == NULL)
		{
			printf("系统出错，自动退出！\n");
			exit();
		}
		else
		{
			while (fscanf(fpu, "%s %s %s", flsu.num, flsu.password, flsu.type) != EOF)
			{
				flag = -1;
				flag_t = -1;
				if (strcmp(flsu.num, lsu.num) != 0 && strcmp(flsu.password, lsu.password) == 0)
				{
					flag = 1;
					flag_t = 1;
					break;
				}
				else if (strcmp(flsu.num, lsu.num) == 0 && strcmp(flsu.password, lsu.password) != 0)
				{
					flag = 1;
					flag_t = 1;
					break;
				}
				else if (strcmp(flsu.num, lsu.num) == 0 && strcmp(flsu.password, lsu.password) == 0)
				{
					flag = 0;
					break;
				}
			}
			fclose(fpu);
			if (flag_t == 1)
			{
				printf("\n学工号或密码错误!\n");
				printf("请重新输入学号\n");
				scanf("%s", lsu.num);
				printf("请重新输入密码\n");
				encrytion(lsu.password);
			}
		}
	} while (flag != 0);
	printf("\n登录成功\n");
	system("pause");
	if (strcmp(flsu.type, "teachar") == 0)
		flag_type = 1;
	else if (strcmp(flsu.type, "student") == 0)
		flag_type = 2;
	interface();
}

void encrytion(char* str)
{
	char temp_password[100];
	int i = 0;
	while ((temp_password[i] = _getch()) != '\r')
	{
		printf("*");
		i++;
	}
	temp_password[i] = '\0';
	strcpy(str, temp_password);
}

void interface()
{
	system("cls");
	if (flag_type == 1)
	{
		int flag_m = 1;
		do
		{
			system("cls");
			printf("[1] 学生信息查找\n");
			printf("[2] 学生信息删除\n");
			printf("[3] 学生信息添加\n");
			printf("[4] 学生信息修改\n");
			printf("[0] 退出程序 \n");
			printf("请输入相关选项 \n");
			int n;
			flag_m = 0;
			scanf("%d", &n);
			switch (n)
			{
			case 1: xsxx_cz();
				break;
			case 2: xsxx_sc();
				break;
			case 3: xsxx_tj();
				break;
			case 4: xsxx_xg();
				break;
			case 0: exit();
				break;
			default: flag_m = 1;
			}
			if (flag_m == 1)
			{
				printf("输入无效，请重新输入 \n\n");
				system("pause");
			}
		} while (flag_m != 0);
	}
	else if (flag_type == 2)
	{
		system("cls");
		xsxx_ll();
	}
}

void xsxx_cz()
{
	char temp_num[50];
	printf("请输入需要查询的学生学号：\n");
	scanf("%s", temp_num);
	struct User* head = NULL;
	struct User* p = NULL;
	struct User* tail = NULL;
	int flag = 1;
	int flag_sel = 1;
	tail = p = head = temp_storage(head);
	while (p != 0)
	{
		if (strcmp(p->num, temp_num) == 0)
		{
			flag = 0;
			printf("学生学号：%s\n", p->num);
			printf("学生姓名：%s\n", p->name);
			printf("成绩：%.2lf\n", p->achie);
			printf("奖学金：%d\n", p->arship);
			break;
		}
		else
		{
			tail = p;
			p = p->next;
		}
	}
	if (flag != 0)
		printf("未找到该学生！\n");
	system("pause");
	do
	{
		system("cls");
		printf("1.返回上一级\n");
		printf("0.退出系统\n\n");
		printf("请输入相关指令编号:");
		int sel;
		flag_sel = 0;
		scanf("%d", &sel);
		switch (sel)
		{
		case 1:	xsxx_cz();
			break;
		case 0: exit();
			break;
		default: flag_sel = 1;
		}
		if (flag_sel == 1)
		{
			printf("无效输入，请重新输入.\n\n");
			exit();
		}
	} while (flag_sel != 0);
}

void xsxx_sc()
{
	char temp_num[50];
	struct User* head = NULL;
	struct User* up, * tail, * kill;
	int no_find_flag = 1;
	tail = head = up = temp_storage(head);
	printf("请输入需要删除的学生学号：\n");
	scanf("%s", temp_num);
	while (up != 0)
	{
		if (strcmp(up->num, temp_num) == 0)
		{
			no_find_flag = 0;
			if (up == head)
				head = head->next;
			else
				tail->next = up->next;
			kill = up;
			up = up->next;
			free(kill);
		}
		else
		{
			tail = up;
			up = up->next;
		}
	}
	if (no_find_flag != 0)
	{
		printf("未找到该用户！\n");
		system("pause");
	}	
	else
	{
		int f = 1;
		int flag = -1;
		do
		{
			printf("保存相关选项？\n");
			printf("[1] 是\n");
			printf("[2] 否\n\n");
			printf("请输入相关选项：\n");
			int sel;
			flag = 0;
			scanf("%d", &sel);
			switch (sel)
			{
			case 1:	break;
				break;
			case 2: exit();
				break;
			default: flag = 1;
			}
			if (1 == flag)
			{
				printf("输入无效！请重新输入：\n\n");
				system("pause");
			}
		} while (flag != 0);
		f = write_data_Student(head);
		if (f == 0)
		{
			printf("删除失败，检查写入文件路径是否正确\n");
			exit();
		}
		else
			printf("删除成功\n");
		system("pause");
	}
	int flag_sel;
	do
	{
		system("cls");
		printf("[1] 返回上一级\n");
		printf("[0] 退出程序\n\n");
		printf("请输入相关选项：");
		int sel;
		flag_sel = 0;
		scanf("%d", &sel);
		switch (sel)
		{
		case 1:	xsxx_sc();
			break;
		case 0: exit();
			break;
		default: flag_sel = 1;
		}
		if (1 == flag_sel)
		{
			printf("输入无效！请重新输入：\n\n");
			system("pause");
		}
	} while (flag_sel != 0);
}

void xsxx_tj()
{
	system("cls");
	struct user *head = NULL;
	struct User temp1;
	struct User temp2;
	printf("请输入学生学号:\n");
	scanf("%s", temp1.num);
	printf("请输入学生姓名:\n");
	scanf("%s", temp1.name);
	printf("请输入学生成绩:\n");
	scanf("%lf", &temp1.achie);
	printf("请输入奖学金数额:\n");
	scanf("%d", &temp1.arship);
	int flag;
	do
	{
		flag = 0;
		FILE* fp = fopen("D://学生信息系统//学生信息.txt", "r");
		while (fscanf(fp, "%s %s %lf %d", temp2.num, temp2.name, &temp2.achie, &temp2.arship) != EOF)
		{
			if (strcmp(temp1.num, temp2.num) == 0)
			{
				flag = 1;
				printf("该学生已存在, 请重新输入！\n");
				printf("请输入学生学号:\n");
				scanf("%s", temp1.num);
				break;
			}
		}
	} while (flag != 0);
	FILE* fq = fopen("D://学生信息系统//学生信息.txt", "a");
	if (fq == NULL)
	{
		printf("文件打开失败!\n");
		exit();
	}
	else
	{
		fprintf(fq, "%s %s %.2lf %d", temp1.num, temp1.name, temp1.achie, temp1.arship);
		fclose(fq);
		printf("添加成功\n");
	}
	system("pause");
	int flag_sel;
	do
	{
		system("cls");
		printf("[1] 返回上一级\n");
		printf("[0] 退出程序\n\n");
		printf("请输入相关选项：");
		int sel;
		flag_sel = 0;
		scanf("%d", &sel);
		switch (sel)
		{
		case 1:	xsxx_tj();
			break;
		case 0: exit();
			break;
		default: flag_sel = 1;
		}
		if (1 == flag_sel)
		{
			printf("输入无效！请重新输入：\n\n");
			system("pause");
		}
	} while (flag_sel != 0);
}

void xsxx_xg()
{
	char temp_num[50];
	char temp_name[50];
	double temp_achie;
	int temp_arship;
	int flag = -1;
	struct User* head = NULL;
	struct User* up, * tail, * kill;
	tail = head = up = temp_storage(head);
	printf("请输入需要修改的学生学号：\n");
	scanf("%s", temp_num);
	while (up != NULL)
	{
		if (strcmp(temp_num, up->num) == 0)
		{
			flag = 0;
			printf("请输入学生学号:\n");
			scanf("%s", temp_num);
			strcpy(up->num, temp_num);
			printf("请输入学生姓名:\n");
			scanf("%s", temp_name);
			strcpy(up->name, temp_name);
			printf("请输入学生成绩:\n");
			scanf("%lf", &temp_achie);
			up->achie = temp_achie;
			printf("请输入奖学金数额:\n");
			scanf("%d", &temp_arship);
			up->arship = temp_arship;
		}
		up = up->next;
	}
	if (flag != 0)
	{
		printf("未找到该用户！\n");
		system("pause");
	}
	else
	{
		int f = 1;
		int flag2 = -1;
		do
		{
			printf("保存相关选项？\n");
			printf("[1] 是\n");
			printf("[2] 否\n\n");
			printf("请输入相关选项：\n");
			int sel;
			flag2 = 0;
			scanf("%d", &sel);
			switch (sel)
			{
			case 1:	break;
				break;
			case 2: exit();
				break;
			default: flag2 = 1;
			}
			if (flag2 == 1)
			{
				printf("输入无效！请重新输入：\n\n");
				system("pause");
			}
		} while (flag2 != 0);
		f = write_data_Student(head);
		if (f == 0)
		{
			printf("修改失败，检查写入文件路径是否正确\n");
			exit();
		}
		else
			printf("修改成功\n");
		system("pause");
	}
	int flag_sel;
	do
	{
		system("cls");
		printf("[1] 返回上一级\n");
		printf("[0] 退出程序\n\n");
		printf("请输入相关选项：");
		int sel;
		flag_sel = 0;
		scanf("%d", &sel);
		switch (sel)
		{
		case 1:	xsxx_xg();
			break;
		case 0: exit();
			break;
		default: flag_sel = 1;
		}
		if (1 == flag_sel)
		{
			printf("输入无效！请重新输入：\n\n");
			system("pause");
		}
	} while (flag_sel != 0);
}

void xsxx_ll()
{
	system("cls");
	struct User* head = NULL;
	struct User* p = NULL;
	p = head = temp_storage(head);
	while (p != NULL)
	{
		printf("%s %s %2.lf %d\n", p->num, p->name, p->achie, p->arship);
		p = p->next;
	}
	system("pause");
	exit();
}

void exit()
{
	printf("感谢使用\n");
	system("pause");
	exit(0);
}

int write_data_Student(struct User* head)
{
	FILE* fp = fopen("D://学生信息系统//学生信息.txt", "w");
	struct User* kill = head;
	if (NULL == fp)
		return 0;
	while (head != 0)
	{
		fprintf(fp, "%s %s %.2lf %d\n", head->num, head->name, head->achie, head->arship);
		head = head->next;
	}
	free_list_User(kill);
	fclose(fp);
	return 1;
}

int write_data_Student_two(struct User* head)
{
	FILE* fp = fopen("D://学生信息系统//学生信息.txt", "a");
	struct User* kill = head;
	if (NULL == fp)
		return 0;
	while (head != 0)
	{
		fprintf(fp, "%s %s %.2lf %d\n", head->num, head->name, head->achie, head->arship);
		head = head->next;
	}
	free_list_User(kill);
	fclose(fp);
	return 1;
}

void free_list_User(struct User* head)
{
	struct User* freeNode;
	while (head != NULL)
	{
		freeNode = head;
		head = head->next;
		free(freeNode);
	}
}

struct User* temp_storage(struct User* head)
{
	FILE* fp = fopen("D://学生信息系统//学生信息.txt", "r");
	if (fp == NULL)
	{
		printf("文件打开失败！\n");
		exit();
	}
	else
	{
		struct User temp;
		struct User* current = NULL, * prev = NULL;
		int i = 0;
		while (fscanf(fp, "%s %s %lf %d", temp.num, temp.name, &temp.achie, &temp.arship) != EOF)
		{
			current = (struct User*)malloc(sizeof(struct User));
			strcpy(current->num, temp.num);
			strcpy(current->name, temp.name);
			current->achie = temp.achie;
			current->arship = temp.arship;
			current->next = NULL;
			if (head == NULL)
				head = current;
			else
				prev->next = current;
			prev = current;
		}
	}
	return head;
}

int main()
{
	logon();
	return 0;
}