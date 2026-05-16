<template>
  <Conf
    ref="conf"
    :type-flag="'cliproxyapi'"
    :default-conf="defaultConf"
    :file="file"
    :file-ext="'yaml'"
    :config-language="'yaml'"
    :show-commond="false"
    url="https://help.router-for.me/configuration/basic.html"
  >
  </Conf>
</template>

<script lang="ts" setup>
  import { ref } from 'vue'
  import Conf from '@/components/Conf/index.vue'
  import { join } from '@/util/path-browserify'
  import { fs } from '@/util/NodeFn'
  import IPC from '@/util/IPC'

  const defaultConf = ref('')
  const conf = ref()
  const file = join(window.Server.BaseDir!, 'cliproxyapi/config.yaml')

  const isZh = window.Server.Lang === 'zh'
  const tmpl = join(
    window.Server.Static!,
    isZh ? 'tmpl/cliproxyapi.zh.yaml' : 'tmpl/cliproxyapi.yaml'
  )
  fs.readFile(tmpl).then((content: string) => {
    defaultConf.value = content
  })

  fs.existsSync(file).then((e) => {
    if (!e) {
      IPC.send('app-fork:cliproxyapi', 'initConfig').then((key: string) => {
        IPC.off(key)
        conf?.value?.update()
      })
    }
  })
</script>
