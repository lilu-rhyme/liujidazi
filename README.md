# 六级搭子

一个给 3-4 个人使用的六级每日互相监督页面。所有人看到同一套任务，但每个人的完成情况独立保存。页面是纯静态文件，部署到 GitHub Pages 后可以免费长期使用。

## 最快的免费上线方式

1. 在 GitHub 新建仓库，把本目录里的 `index.html` 和 `README.md` 上传。
2. 仓库 Settings -> Pages -> Deploy from branch，选择 `main` 和 `/ (root)`，保存后等待生成网址。
3. 想要多人同步时，在 Supabase 免费版创建项目，在 SQL Editor 执行：

```sql
create table public.room_state (
  id text primary key,
  state jsonb not null default '{"done":[false,false,false,false]}'::jsonb,
  updated_at timestamptz not null default now()
);
alter table public.room_state enable row level security;
create policy "public read room state" on public.room_state for select using (true);
create policy "public write room state" on public.room_state for insert with check (true);
create policy "public update room state" on public.room_state for update using (true) with check (true);
```

4. 打开 `index.html`，把 `SUPABASE_URL` 和 `SUPABASE_KEY` 替换成 Supabase Project Settings -> API 里的 URL 和 `anon public` key，再上传一次。
5. 你们几个人打开同一个网址即可。房间码默认是 `CET-7F2K`，首次打开时输入自己的昵称；之后每个人只会看到和修改自己的完成情况，同时可以看到搭子的进度。
6. 页面支持多个房间：点击“新建房间”会生成新的房间链接，例如 `?room=CET-AB12`。不同房间的数据相互隔离，点击“复制邀请链接”发给对应搭子即可。
7. 每个房间都可以点击“编辑任务”修改四项任务的名称和说明，修改只影响当前房间。

不配置 Supabase 也能使用，但每个人只能看到自己浏览器里的打卡记录。Supabase 的 anon key 可以放在前端，千万不要把 service_role key 放进页面。
