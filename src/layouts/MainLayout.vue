<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="bg-primary text-white">
      <q-toolbar class="bg-green">
        <q-toolbar-title>
          <q-avatar>
            <img src="../../images/earth-americas-solid.svg" />
          </q-avatar>
          Translator
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>
