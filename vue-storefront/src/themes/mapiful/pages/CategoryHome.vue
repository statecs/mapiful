<template>
  <div id="home">
    <no-ssr>
      <main-slider />
    </no-ssr>

    <section class="new-collection container px15">
      <div>
        <header class="col-md-12">
          <h4 class="align-left cl-accent">
            {{ $t("Shop Prints Range") }}
          </h4>
        </header>
      </div>
    </section>

    <section v-if="isOnline" class="container pb60 px15">
      <tile-links />
    </section>

    <section class="new-collection container px15">
      <div>
        <header class="col-md-12">
          <h4 class="align-left cl-accent">
            {{ $t("Featured products") }}
          </h4>
        </header>
      </div>
      <div id="category">
        <div class="container pb60">
          <div class="row m0 pt15">
            <div class="px10 border-box products-list">
              <div v-if="isCategoryEmpty" class="hidden-xs">
                <h4 data-testid="noProductsInfo">
                  {{ $t("No products found!") }}
                  <button @click="loadProducts()">Load products</button>
                </h4>
              </div>
              <lazy-hydrate
                :trigger-hydration="!loading"
                v-if="isLazyHydrateEnabled"
              >
                <product-listing
                  :columns="defaultColumn"
                  :products="getCategoryProducts"
                />
              </lazy-hydrate>
              <product-listing
                v-else
                :columns="defaultColumn"
                :products="getCategoryProducts"
              />
            </div>
          </div>
        </div>
      </div>
    </section>

    <no-ssr>
      <review-slider />
    </no-ssr>

    <Onboard />
  </div>
</template>


<script>
// query constructor
import { isServer, onlineHelper } from "@vue-storefront/core/helpers";

// Theme core components
import ProductListing from "theme/components/core/ProductListing";
import HeadImage from "theme/components/core/blocks/MainSlider/HeadImage";
import MainSlider from "theme/components/core/blocks/MainSlider/MainSlider";
import ReviewSlider from "theme/components/core/blocks/ReviewSlider/ReviewSlider";
import CategoryList from "theme/pages/CategoryList";

// Theme local components
import Onboard from "theme/components/theme/blocks/Home/Onboard";
import PromotedOffers from "theme/components/theme/blocks/PromotedOffers/PromotedOffers";
import TileLinks from "theme/components/theme/blocks/TileLinks/TileLinks";
import { Logger } from "@vue-storefront/core/lib/logger";
import { mapGetters } from "vuex";
import config from "config";
import { registerModule } from "@vue-storefront/core/lib/modules";
import { RecentlyViewedModule } from "@vue-storefront/core/modules/recently-viewed";

import LazyHydrate from "vue-lazy-hydration";
import Sidebar from "../components/core/blocks/Category/Sidebar.vue";
import Breadcrumbs from "../components/core/Breadcrumbs.vue";
import SortBy from "../components/core/SortBy.vue";
import { getSearchOptionsFromRouteParams } from "@vue-storefront/core/modules/catalog-next/helpers/categoryHelpers";
import Columns from "../components/core/Columns.vue";
import ButtonFull from "theme/components/theme/ButtonFull.vue";
import onBottomScroll from "@vue-storefront/core/mixins/onBottomScroll";
import rootStore from "@vue-storefront/core/store";
import { catalogHooksExecutors } from "@vue-storefront/core/modules/catalog-next/hooks";
import {
  localizedRoute,
  currentStoreView,
} from "@vue-storefront/core/lib/multistore";
import { htmlDecode } from "@vue-storefront/core/filters";

const THEME_PAGE_SIZE = 50;

const composeInitialPageState = async (store, route, forceLoad = false) => {
  try {
    const filters = getSearchOptionsFromRouteParams(route.params);
    const cachedCategory = store.getters["category-next/getCategoryFrom"](
      route.path
    );
    const currentCategory =
      cachedCategory && !forceLoad
        ? cachedCategory
        : await store.dispatch("category-next/loadCategory", { filters });
    const pageSize = store.getters["url/isBackRoute"]
      ? store.getters["url/getCurrentRoute"].categoryPageSize
      : THEME_PAGE_SIZE;
    await store.dispatch("category-next/loadCategoryProducts", {
      route,
      category: currentCategory,
      pageSize,
    });
    const breadCrumbsLoader = store.dispatch(
      "category-next/loadCategoryBreadcrumbs",
      {
        category: currentCategory,
        currentRouteName: currentCategory.name,
        omitCurrent: true,
      }
    );

    if (isServer) await breadCrumbsLoader;
    catalogHooksExecutors.categoryPageVisited(currentCategory);
  } catch (e) {
    Logger.error(
      "Problem with setting Category initial data!",
      "category",
      e
    )();
  }
};

export default {
  components: {
    LazyHydrate,
    ButtonFull,
    ProductListing,
    Breadcrumbs,
    Sidebar,
    SortBy,
    Columns,
    CategoryList,
    MainSlider,
    ReviewSlider,
    HeadImage,
    Onboard,
    PromotedOffers,
    TileLinks,
  },
  mixins: [onBottomScroll],
  data() {
    return {
      mobileFilters: false,
      defaultColumn: 4,
      loadingProducts: false,
      loading: true,
    };
  },
  computed: {
    ...mapGetters("user", ["isLoggedIn"]),
    ...mapGetters("homepage", ["getEverythingNewCollection"]),
    categories() {
      return this.getCategories;
    },
    ...mapGetters("category-next", ["getCategoryProducts"]),
    ...mapGetters({
      getCurrentSearchQuery: "category-next/getCurrentSearchQuery",
      getCategoryProducts: "category-next/getCategoryProducts",
      getCurrentCategory: "category-next/getCurrentCategory",
      getCategoryProductsTotal: "category-next/getCategoryProductsTotal",
      getAvailableFilters: "category-next/getAvailableFilters",
    }),
    isOnline() {
      return onlineHelper.isOnline;
    },
    isLazyHydrateEnabled() {
      return config.ssr.lazyHydrateFor.includes("category-next.products");
    },
    isCategoryEmpty() {
      return this.getCategoryProductsTotal === 0;
    },
  },
  async asyncData({ store, route, context }) {
    // this is for SSR purposes to prefetch data - and it's always executed before parent component methods
    if (context) context.output.cacheTags.add("category");
    await composeInitialPageState(store, route);
  },
  async beforeRouteEnter(to, from, next) {
    if (isServer) next();
    // SSR no need to invoke SW caching here
    else if (!from.name) {
      // SSR but client side invocation, we need to cache products and invoke requests from asyncData for offline support
      next(async (vm) => {
        vm.loading = true;
        await composeInitialPageState(vm.$store, to, true);
        await vm.$store.dispatch("category-next/cacheProducts", { route: to }); // await here is because we must wait for the hydration
        vm.loading = false;
      });
    } else {
      // Pure CSR, with no initial category state
      next(async (vm) => {
        vm.loading = true;
        vm.$store.dispatch("category-next/cacheProducts", { route: to });
        vm.loading = false;
      });
    }
  },
  methods: {
    loadProducts() {
      this.$router.push({
        path: "/category",
        name: "Category",
        params: { userId: "123" },
      });
    },
    openFilters() {
      this.mobileFilters = true;
    },
    closeFilters() {
      this.mobileFilters = false;
    },
    async changeFilter(filterVariant) {
      this.$store.dispatch("category-next/switchSearchFilters", [
        filterVariant,
      ]);
    },
    columnChange(column) {
      this.defaultColumn = column;
    },
    async onBottomScroll() {
      if (this.loadingProducts) return;
      this.loadingProducts = true;
      try {
        await this.$store.dispatch("category-next/loadMoreCategoryProducts");
      } catch (e) {
        Logger.error("Problem with fetching more products", "category", e)();
      } finally {
        this.loadingProducts = false;
      }
    },
  },
  metaInfo() {
    const storeView = currentStoreView();
    const {
      meta_title,
      meta_description,
      name,
      slug,
    } = this.getCurrentCategory;
    const meta = meta_description
      ? [
          {
            vmid: "description",
            name: "description",
            content: htmlDecode(meta_description),
          },
        ]
      : [];
    /* const categoryLocaliedLink = localizedRoute({
      name: 'category-amp',
      params: { slug }
    }, storeView.storeCode)
    const ampCategoryLink = this.$router.resolve(categoryLocaliedLink).href */

    return {
      // link: [ { rel: 'amphtml', href: ampCategoryLink } ],
      title: htmlDecode(meta_title || name),
      meta,
    };
  },
};
</script>

<style lang="scss" scoped>
.btn {
  &__filter {
    min-width: 100px;
  }
}
.divider {
  width: calc(100vw - 8px);
  bottom: 20px;
  left: -36px;
}
.category-filters {
  width: 242px;
}

.mobile-filters {
  display: none;
  overflow: auto;
}

.mobile-filters-button {
  display: none;
}

.mobile-sorting {
  display: none;
}

.category-title {
  line-height: 65px;
}

.sorting {
  label {
    margin-right: 10px;
  }
}

@media (max-width: 64em) {
  .products-list {
    max-width: 530px;
  }
}

@media (max-width: 770px) {
  .category-title {
    margin: 0;
    font-size: 36px;
    line-height: 40px;
  }

  .products-list {
    width: 100%;
    max-width: none;
  }

  .mobile-filters {
    display: block;
  }

  .mobile-filters-button {
    display: block;
    height: 45px;
  }

  .sorting {
    display: none;
  }

  .mobile-sorting {
    display: block;
  }

  .category-filters {
    display: none;
  }

  .product-listing {
    justify-content: center;
  }

  .mobile-filters {
    position: fixed;
    background-color: #f2f2f2;
    z-index: 5;
    padding: 0 40px;
    left: 0;
    width: 100vw;
    height: 100vh;
    top: 0;
    box-sizing: border-box;
  }

  .mobile-filters-body {
    padding-top: 50px;
  }
}

.close-container {
  left: 0;
}

.close {
  margin-left: auto;
}
</style>
<style lang="scss">
.product-image {
  max-height: unset !important;
}
</style>
