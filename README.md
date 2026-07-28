# 测试
`OnePlus Ace 3 Android 15(Vanilla Ice Cream) android13-5.15 gsi`

步骤 `Fallback build kernel`, `Build kernel` 被跳过。
```
source/fs/proc/task_mmu.c:326:9: error: variable 'dentry' is used uninitialized whenever 'if' condition is false [-Werror,-Wsometimes-uninitialized]
                                if (spoofed_redirected_name)
                                    ^~~~~~~~~~~~~~~~~~~~~~~
source/fs/proc/task_mmu.c:328:13: note: uninitialized use occurs here
        if (dentry) {
            ^~~~~~
source/fs/proc/task_mmu.c:326:5: note: remove the 'if' if its condition is always true
                                if (spoofed_redirected_name)
                                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~
source/fs/proc/task_mmu.c:311:23: note: initialize the variable 'dentry' to silence this warning
        struct dentry *dentry;
                             ^
                              = NULL
1 error generated.
make[2]: *** [/home/runner/work/Action_OnePlus_MKSU_SUSFS/Action_OnePlus_MKSU_SUSFS/kernel_workspace/kernel_platform/common/scripts/Makefile.build:287: fs/proc/task_mmu.o] Error 1
make[1]: *** [/home/runner/work/Action_OnePlus_MKSU_SUSFS/Action_OnePlus_MKSU_SUSFS/kernel_workspace/kernel_platform/common/scripts/Makefile.build:549: fs/proc] Error 2
make: *** [/home/runner/work/Action_OnePlus_MKSU_SUSFS/Action_OnePlus_MKSU_SUSFS/kernel_workspace/kernel_platform/common/Makefile:1963: fs] Error 2
make: *** Waiting for unfinished jobs....
```
```
source/mm/memory.c:7262:30: error: implicit declaration of function 'SUSFS_IS_INODE_SUS_MAP' [-Werror,-Wimplicit-function-declaration]
                if (vma && vma->vm_file && SUSFS_IS_INODE_SUS_MAP(file_inode(vma->vm_file)))
                                           ^
1 error generated.
make[1]: *** [/home/runner/work/Action_OnePlus_MKSU_SUSFS/Action_OnePlus_MKSU_SUSFS/kernel_workspace/kernel_platform/common/scripts/Makefile.build:287: mm/memory.o] Error 1
make: *** [/home/runner/work/Action_OnePlus_MKSU_SUSFS/Action_OnePlus_MKSU_SUSFS/kernel_workspace/kernel_platform/common/Makefile:1963: mm] Error 2
```
总结环节：
```

start date:2026_07_28 03:34:15
end date:2026_07_28 03:36:52
build total time:
(02:37 (mm:ss))
 

```
步骤 Make AnyKernel3
```
Run git clone https://github.com/Numbersf/AnyKernel3 --depth=1
  git clone https://github.com/Numbersf/AnyKernel3 --depth=1
  rm -rf ./AnyKernel3/.git
  
  dir1="kernel_workspace/kernel_platform/out/msm-kernel-kalama-gki/dist/"
  dir2="kernel_workspace/kernel_platform/bazel-out/k8-fastbuild/bin/msm-kernel/kalama_gki_kbuild_mixed_tree/"
  dir3="kernel_workspace/kernel_platform/out/msm-kalama-kalama-gki/dist/"
  dir4="kernel_workspace/kernel_platform/out/msm-kernel-kalama-gki/gki_kernel/common/arch/arm64/boot/"
  dir5="kernel_workspace/kernel_platform/out/msm-kalama-kalama-gki/gki_kernel/common/arch/arm64/boot/"
  target1="./AnyKernel3/"
  target2="./kernel_workspace/kernel"
  
  # 查找 Image 文件
  if find "$dir1" -name "Image" | grep -q "Image"; then
    image_path="$dir1"Image
  elif find "$dir2" -name "Image" | grep -q "Image"; then
    image_path="$dir2"Image
  elif find "$dir3" -name "Image" | grep -q "Image"; then
    image_path="$dir3"Image
  elif find "$dir4" -name "Image" | grep -q "Image"; then
    image_path="$dir4"Image
  elif find "$dir5" -name "Image" | grep -q "Image"; then
    image_path="$dir5"Image
  else
    image_path=$(find "./kernel_workspace/kernel_platform/common/out/" -name "Image" | head -n 1)
  fi
  
  # 拷贝 Image
  if [ -n "$image_path" ] && [ -f "$image_path" ]; then
    mkdir -p "$dir1"
    if [ "$(realpath "$image_path")" != "$(realpath "$dir1"Image)" ]; then
      cp "$image_path" "$dir1"
    else
      echo "源文件与目标相同，跳过复制"
    fi
    cp "$dir1"Image ./AnyKernel3/Image
  else
    echo "未找到 Image 文件，构建可能失败"
    exit 1
  fi
  
  # 可选复制其它新文件（如果存在）
  if [ "sm8550" = "sm8750" ] || [ "sm8550" = "sm8850" ]; then
  for file in dtbo.img system_dlkm.erofs.img vendor_dlkm.img vendor_boot.img; do
    if [ -f "$dir1$file" ]; then
      target_name="$file"
      # 特殊处理 system_dlkm.erofs.img 的目标名
      if [ "$file" = "system_dlkm.erofs.img" ]; then
        target_name="system_dlkm.img"
      fi
      cp "$dir1$file" "./AnyKernel3/$target_name"
    else
      echo "$file 不存在，跳过复制"
    fi
  done
  fi
  shell: /usr/bin/bash -e {0}
  env:
    KSUVER: 14277
Cloning into 'AnyKernel3'...
find: ‘kernel_workspace/kernel_platform/out/msm-kernel-kalama-gki/dist/’: No such file or directory
find: ‘kernel_workspace/kernel_platform/bazel-out/k8-fastbuild/bin/msm-kernel/kalama_gki_kbuild_mixed_tree/’: No such file or directory
find: ‘kernel_workspace/kernel_platform/out/msm-kalama-kalama-gki/dist/’: No such file or directory
find: ‘kernel_workspace/kernel_platform/out/msm-kernel-kalama-gki/gki_kernel/common/arch/arm64/boot/’: No such file or directory
find: ‘kernel_workspace/kernel_platform/out/msm-kalama-kalama-gki/gki_kernel/common/arch/arm64/boot/’: No such file or directory
find: ‘./kernel_workspace/kernel_platform/common/out/’: No such file or directory
未找到 Image 文件，构建可能失败
Error: Process completed with exit code 1.
```

workflow job 于此失败，耗时 9m 27s。

在其他测试，会在 `Initialize repo and sync` 步骤卡住 1h 29m，并在 `Force remove -dirty suffix` 失败。
```
warning: adding embedded git repository: .repo/manifests
hint: You've added another git repository inside your current repository.
hint: Clones of the outer repository will not contain the contents of
hint: the embedded repository and will not know how to obtain it.
hint: If you meant to add a submodule, use:
hint:
hint: 	git submodule add <url> .repo/manifests
hint:
hint: If you added this path by mistake, you can remove it from the
hint: index with:
hint:
hint: 	git rm --cached .repo/manifests
hint:
hint: See "git help submodule" for more information.
hint: Disable this message with "git config set advice.addEmbeddedRepo false"
fatal: sha1 file '.git/objects/pack/tmp_pack_1cbWyi' write error. Out of diskspace
Error: Process completed with exit code 128.
```



# 原始 readme 内容

## 食用说明
一加内核开源地址：[OnePlusOSS](https://github.com/OnePlusOSS/kernel_manifest)

## 原始项目
[MKSU-SKN](https://github.com/ShirkNeko/KernelSU)
