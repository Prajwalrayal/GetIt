<template>
  <div class="lg:flex my-16 md:mx-32 m-4">
    <section class="w-full content-center">
      <div class="flex flex-col gap-4">
        <span class="text-2xl font-bold">Your Orders</span>
        <hr />
        <div
          class="p-4 flex flex-col gap-2 rounded-xl shadow-md"
          v-for="(order, index) in orders"
          :key="index"
        >
          <div class="flex justify-between max-md:flex-col gap-2">
            <div class="flex gap-10">
              <div class="flex flex-col gap-1">
                <p class="font-semibold text-gray-700">Order Placed</p>
                <p class="text-sm">{{ order.created_at.slice(0, 10) }}</p>
              </div>
              <div class="flex flex-col gap-1">
                <p class="font-semibold text-gray-700">Email</p>
                <p class="text-sm">{{ order.buyer }}</p>
              </div>
            </div>
            <div>
              <p class="font-semibold text-gray-700">Order Id</p>
              <p class="text-sm"># {{ order.id }}</p>
            </div>
          </div>
          <hr />
          <div>
            <div
              class="flex mt-2"
              v-for="(orderItem, index) in order.order_items"
              :key="index"
            >
              <img
                class="size-24 object-cover rounded-xl"
                :src="orderItem.ticket.event.cover_image"
                alt="Watch"
              />

              <div class="px-4 flex justify-between w-full">
                <div>
                  <h5 class="font-bold">{{ orderItem.ticket.event.title }}</h5>
                  <p class="text-gray-500">
                    Quantity : {{ orderItem.quantity }}
                  </p>
                  <p
                    v-if="order.status === 'PENDING'"
                    class="flex text-gray-500"
                  >
                    Payment :
                    <img
                      src="../assets/warning.png"
                      class="size-5 mt-0.5 m-2"
                    />
                    <span class="font-semibold">Pending</span>
                  </p>
                  <p
                    v-else-if="order.status === 'COMPLETED'"
                    class="flex text-gray-500"
                  >
                    Payment :
                    <img src="../assets/check.png" class="size-5 mt-0.5 m-2" />
                    <span class="font-semibold">Completed</span>
                  </p>
                  <p
                    v-else="order.status === 'FAILED'"
                    class="flex text-gray-500"
                  >
                    Payment :
                    <img src="../assets/failed.png" class="size-5 mt-0.5 m-2" />
                    <span class="font-semibold">Failed</span>
                  </p>
                  <p class="mt-4 font-bold">{{ order.price }}</p>
                </div>
                <div>
                  <span class="font-bold"
                    >₹ {{ orderItem.ticket.event.ticket_price }}</span
                  >
                </div>
              </div>
            </div>
          </div>
          <hr class="my-2" />
          <div
            class="flex max-md:flex-col md:gap-10 gap-2 justify-between w-full"
          >
            <div class="w-full flex-col">
              <div class="flex">
                <div class="w-full">
                  <p class="font-bold text-black-700 mb-1">Billing Address</p>
                  <p class="mb-2">{{ order.buyer_name }}</p>
                  <p>{{ order.address }}</p>
                </div>

                <div class="flex flex-col gap-2">
                  <div class="flex gap-20 justify-between text-gray-600">
                    <p>Subtotal</p>
                    <p>₹{{ order.total }}</p>
                  </div>
                  <div class="flex gap-20 justify-between text-gray-600">
                    <p>Shipping</p>
                    <p>₹0</p>
                  </div>
                  <div class="flex gap-20 justify-between font-bold">
                    <p>Total</p>
                    <p>₹ {{ order.total }}</p>
                  </div>
                  <div class="flex gap-20 justify-between font-bold"></div>
                </div>
              </div>
              <div class="flex flex-row-reverse">
                <a :href="`${order.payment_url}`">
                  <button
                    v-if="order.status === 'PENDING'"
                    class="text-white bg-[#ea454c] mt-3 -ml-1 rounded-2xl w-64 h-10"
                  >
                    <p class="font-bold">Retry Payment</p>
                  </button>
                </a>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>
<script setup>
import { ref, onMounted } from "vue";
import { useAuthStore } from "../../store/auth";
import { useNuxtApp } from "#app";
import { useRouter } from "vue-router";

const config = useRuntimeConfig();
const router = useRouter();
const orders = ref({});
const authStore = useAuthStore();

onMounted(async () => {
  const nuxtApp = useNuxtApp();

  try {
    const response = await nuxtApp.$authenticatedFetch(
      `${config.public.API_BASE_URL}/api/orders/`
    );

    orders.value = response;
    orders.value.reverse();
  } catch (error) {
    console.log("Error", error);
    toast.error("Error fetching orders", {
      autoClose: 2000,
      position: toast.POSITION.BOTTOM_CENTER,
    });
  }
});

if (!authStore.isAuthenticated) {
  router.push("/Signin");
}
</script>
