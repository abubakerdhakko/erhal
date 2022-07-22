<template>
  <div class="mt-20">
    <div style="margin-bottom: 40px">
      <span class="head-title">{{
        this.$route.params.viewFlag
          ? "Detail Asset Category Template"
          : this.$route.params.editFlag
          ? "Edit Asset Category Template"
          : "Create Asset Category Template"
      }}</span>
    </div>
    <div class="main-wrapper">
      <div class="cate-field">
        <SInput
          label="Template Name*"
          v-model="categoryName"
          :error="getFormErrorMessage($v.categoryName)"

        />
      </div>
      <!-- <div class="full-row subtitle mt-8 mb-8">Description</div> -->
    </div>
    <div class="mt-20">
      <SAccordion
        title="Additional Details"
        expandIcon="arrows"
        padding="48"
        :open="
          this.$route.params.viewFlag || this.$route.params.editFlag
            ? true
            : false
        "
        :shadow="true"
      >
        <div class="custom-btn-main">
          <div class="flex-last">
            <div class="padding-btn">
              <SButton
                :dropdown="false"
                :title="'+'"
                @clicked-item="addCategoryField"
              ></SButton>
            </div>
          </div>
          <div
            class="custom-fields pt-8 mb-8"
            v-for="(item, index) in createCateObj.categoryFields"
            :key="index"
          >
            <div class="label-field">
              <SInput
                label="Field Label"
                v-model="item.label"
                :error="
                  getFormErrorMessage(
                    $v.createCateObj.categoryFields.$each[index].label
                  )
                "
              />
            </div>
            <div class="type_field">
              <SSelect
                :label="'Type'"
                v-model="item.type"
                :source="createCateObj.fieldType"
                resultsDisplay="name"
                resultsValue="value"
                :autocomplete="true"
                @selected="
                  (value) => {
                    addFieldType(value, index);
                  }
                "
                @clear="clearSelectedFieldType(index)"
                :error="
                  getFormErrorMessage(
                    $v.createCateObj.categoryFields.$each[index].type
                  )
                "
                :key="updateComp + 'typeField'"
              />
            </div>
            <div class="option-field">
              <SInput
                label="Options (comma seprated)"
                :disabled="item.type !== 'select'"
                v-model="item.options"
                :error="
                  getFormErrorMessage(
                    $v.createCateObj.categoryFields.$each[index].options
                  )
                "
                :ref="`options${index}`"
              />
            </div>
            <div class="check-box">
              <span>Mandatory</span>
              <div class="check-box">
                <SCheckBox
                  type="checkbox"
                  v-model="item.mandatory"
                  @input="mandatoryYesHandler(item.mandatory, index)"
                />
              </div>
            </div>
            <div class="pt-15 flex-last pr-acc-btn-cros">
              <img
                src="/assets/icons/Close.svg"
                alt=""
                width="20px"
                style="cursor: pointer"
                @click="subCategoryField(index)"
              />
            </div>
          </div>
        </div>
      </SAccordion>
    </div>

    <div class="mt-20">
      <SAccordion
        title="Issue Categories and Types"
        expandIcon="arrows"
        padding="48"
        :open="
          this.$route.params.viewFlag || this.$route.params.editFlag
            ? true
            : false
        "
        :shadow="true"
      >
        <div class="custom-btn-main">
          <div class="flex-last">
            <div class="padding-btn">
              <SButton
                :dropdown="false"
                :title="'+'"
                @clicked-item="addissueTypesField"
              ></SButton>
            </div>
          </div>
          <div
            class="custom-fields pt-8 mb-8"
            v-for="(item, index) in createIssueTypeObj.IssueTypeFields"
            :key="index"
          >
            <div class="label-field">
              <SInput
                label="Category Name"
                v-model="item.label"
                :error="
                  getFormErrorMessage(
                    $v.createIssueTypeObj.IssueTypeFields.$each[index].label
                  )
                "
              />
            </div>

            <div class="option-field">
              <SInput
                label="Types (comma seprated)"
                v-model="item.options"
                :error="
                  getFormErrorMessage(
                    $v.createIssueTypeObj.IssueTypeFields.$each[index].options
                  )
                "
                :ref="`options${index}`"
              />
            </div>
            <div class=""></div>

            <div class="pt-15 flex-last pr-acc-btn-cros">
              <img
                src="/assets/icons/Close.svg"
                alt=""
                width="20px"
                style="cursor: pointer"
                @click="subIssueTypeField(index)"
              />
            </div>
          </div>
        </div>
      </SAccordion>
    </div>
    <div class="button-row mt-20">
      <div class="item1">
        <SButton
          v-if="!this.$route.params.editFlag"
          :dropdown="false"
          :title="'Save'"
          @clicked-item="saveAddCategory"
        ></SButton>
      </div>
      <div class="item1">
        <SButton
          v-if="this.$route.params.editFlag"
          :dropdown="false"
          :title="'Update'"
          @clicked-item="updateAddCategory"
        ></SButton>
      </div>
    </div>
    <SToast
      :message="toastMessage"
      :time-out="3000"
      :type="toastType"
      :key="toastFlag"
    />
    <!-- Loader Component -->
    <loader v-if="loaderFlag" />
    <!-- Loader Component -->
  </div>
</template>

<script>
import AssetManagementService from "../../services/AssetManagementService";
import { required, maxLength } from "vuelidate/lib/validators";
import { Functions } from "@/shared/Functions";
import loader from "@/components/Loader.vue";
export default {
  name: "createCategory",
  data() {
    return {
      cateObject: {
        categoryTemplate: {
          uuid: 511229637231593947,
          name: "Postman 17 edit test template",
          tenantUuid: "TEST-ORG",
          createdBy: "waseem haider",
          lastUpdateBy: "Abubaker",
          archived: false,
          categoryFiled: {
            type: "aaa",
            fields: [
              {
                label: "ff11",
                type: "text",
                fieldPosition: "",
                mandatory: false,
                options: "opt11",
              },
              {
                label: "ff22",
                type: "text",
                fieldPosition: "",
                mandatory: true,
                options: "opt22",
              },
            ],
          },
          issuesCategory: {
            type: "aaa",
            fields: [
              {
                label: "ff22",
                type: "text",
                fieldPosition: "",
                mandatory: false,
                options: "opt22",
              },
              {
                label: "ff22",
                type: "text",
                fieldPosition: "",
                mandatory: false,
                options: "opt22",
              },
              {
                label: "ff22",
                type: "text",
                fieldPosition: "",
                mandatory: false,
                options: "opt22",
              },
            ],
          },
        },
      },
      toastFlag: 0,
      toastMessage: "",
      toastType: "",
      currentUserDetails: "",
      categoryName: "",
      loaderFlag: false,
      createCateObj: {
        fieldType: [
          { name: "Text", value: "text" },
          { name: "Select", value: "select" },
        ],
        categoryFields: [],
      },
      createIssueTypeObj: {
        IssueTypeFields: [],
      },
      // createIssueTypeObj.IssueTypeFields
      updateComp: 0,
    };
  },
  methods: {
    clearCategoryName(){
      this.categoryName = ""
    },
    getFormErrorMessage(fieldValidation) {
      if (fieldValidation.$dirty) {
        return Functions.getFormFieldErrorMessage(fieldValidation);
      }
    },
    addFieldType(value, index) {
      this.createCateObj.categoryFields[index].type =
        value.selectedObject.value;
    },
    clearSelectedFieldType(index) {
      this.createCateObj.categoryFields[index].type = "";
      this.updateComp++;
    },
    subCategoryField(index) {
      this.createCateObj.categoryFields.splice(index, 1);
    },
    subIssueTypeField(index) {
      this.createIssueTypeObj.IssueTypeFields.splice(index, 1);
    },
    close() {
      this.$emit("close");
    },
    mandatoryYesHandler(evt, index) {
      this.createCateObj.categoryFields[index].mandatory = evt;
    },
    addCategoryField() {
      this.createCateObj.categoryFields.push({
        label: "",
        type: "",
        position: "",
        mandatory: false,
        options: "",
      });
    },
    addissueTypesField() {
      this.createIssueTypeObj.IssueTypeFields.push({
        label: "",
        position: "",
        options: "",
      });
    },
    saveAddCategory() {
      this.$v.$touch();
      if (this.$v.$invalid) {
        this.$emit("showToast", "Please fill all required fields", "warning");
        return;
      } else {
        let valdiation = this.customOptionValdiationCheck();
        if (!valdiation) {
          var value = this.createCateObj;
          var val = this.createIssueTypeObj;
          this.loaderFlag = true;
          let addCategory = {
            categoryTemplate: {
              tenantUuid: this.currentUserDetails.profile.organizationId,
              name: this.categoryName,
              createdBy: `${this.currentUserDetails.profile.firstName} ${this.currentUserDetails.profile.lastName}`,
              lastUpdateBy: `${this.currentUserDetails.profile.firstName} ${this.currentUserDetails.profile.lastName}`,
              archived: false,
              categoryFiled: {
                type: this.categoryName + " field template",
                fields: [],
              },
              issuesCategory: {
                type: this.categoryName + " field template",
                fields: [],
              },
            },
          };

          let fieldss = [];
          for (let index = 0; index < value.categoryFields.length; index++) {
            let addCategoryField = value.categoryFields[index];
            let field = {};
            field.label = addCategoryField.label;
            field.type = addCategoryField.type;
            field.fieldPosition = addCategoryField.position;
            field.mandatory = addCategoryField.mandatory;
            field.options = addCategoryField.options;
            fieldss.push(field);
          }
          let issueFields = [];
          for (let index = 0; index < val.IssueTypeFields.length; index++) {
            let addIssueTypeField = val.IssueTypeFields[index];
            let field = {};
            field.label = addIssueTypeField.label;
            field.options = addIssueTypeField.options;
            (field.type = ""),
              (field.fieldPosition = ""),
              (field.mandatory = false),
              issueFields.push(field);
          }

          addCategory.categoryTemplate.categoryFiled.fields = fieldss;
          addCategory.categoryTemplate.issuesCategory.fields = issueFields;

          AssetManagementService.addCategoryTemplate(addCategory)
            .then((response) => {
              if (response.data.responseIdentifier === "Success") {
                this.loaderFlag = false;
                this.categoryPopUp = false;
                this.showToast("Category added successfully", "success");
                this.$router.push({
                  name: "asset-category-templates",
                });
              }
            })
            .catch((error, response) => {
              if (error) {
                this.loaderFlag = false;
                this.showToast("Error", "error");
              }
            });
        }
      }
    },

    updateAddCategory() {
      this.$v.$touch();
      if (this.$v.$invalid) {
        this.$emit("showToast", "Please fill all required fields", "warning");
        return;
      } else {
        let valdiation = this.customOptionValdiationCheck();
        if (!valdiation) {
          var value = this.createCateObj;
          var val = this.createIssueTypeObj;
          this.loaderFlag = true;
          let editCategory = {
            categoryTemplate: {
              uuid: this.$route.params.assetID,
              tenantUuid: this.currentUserDetails.profile.organizationId,
              name: this.categoryName,
              createdBy: `${this.currentUserDetails.profile.firstName} ${this.currentUserDetails.profile.lastName}`,
              lastUpdateBy: `${this.currentUserDetails.profile.firstName} ${this.currentUserDetails.profile.lastName}`,
              archived: false,
              categoryFiled: {
                type: this.categoryName + " field template",
                fields: [],
              },
              issuesCategory: {
                type: this.categoryName + " field template",
                fields: [],
              },
            },
          };

          let fieldss = [];
          for (let index = 0; index < value.categoryFields.length; index++) {
            let addCategoryField = value.categoryFields[index];
            let field = {};
            field.label = addCategoryField.label;
            field.type = addCategoryField.type;
            field.fieldPosition = addCategoryField.position;
            field.mandatory = addCategoryField.mandatory;
            field.options = addCategoryField.options;
            fieldss.push(field);
          }
          let issueFields = [];
          for (let index = 0; index < val.IssueTypeFields.length; index++) {
            let addIssueTypeField = val.IssueTypeFields[index];
            let field = {};
            field.label = addIssueTypeField.label;
            field.options = addIssueTypeField.options;
            (field.type = ""),
              (field.fieldPosition = ""),
              (field.mandatory = false),
              issueFields.push(field);
          }
          editCategory.categoryTemplate.categoryFiled.fields = fieldss;
          editCategory.categoryTemplate.issuesCategory.fields = issueFields;
          // AssetManagementService.updateCategoryTemplate(editCategory)
          //   .then((response) => {
          //     if (response.data.responseIdentifier === "Success") {
          //       this.loaderFlag = false;
          //       this.showToast("Category added successfully", "success");
          //       this.$router.push({
          //         name: "asset-category-templates",
          //       });
          //     }
          //   })
          //   .catch((error, response) => {
          //     if (error) {
          //       this.loaderFlag = false;
          //       this.showToast("Error", "error");
          //     }
          //   });
        }
      }
    },

    customOptionValdiationCheck() {
      if (
        this.createCateObj.categoryFields &&
        this.createCateObj.categoryFields.length > 0
      ) {
        let count = 0;
        for (var object of this.createCateObj.categoryFields) {
          count++;
          if (object.type == "select" && object.options == "") {
            this.showToast(
              "Please fill options field in Additional Field #" + count,
              "warning"
            );
            return true;
          }
        }
      } else if (
        this.createIssueTypeObj.issueTypeFields &&
        this.createIssueTypeObj.issueTypeFields.length > 0
      ) {
        let count = 0;
        for (var obj of this.createIssueTypeObj.issueTypeFields) {
          count++;
          if (obj.type == "select" && object.options == "") {
            this.showToast(
              "Please fill options field in Additional Field #" + count,
              "warning"
            );
            return true;
          }
        }
      }
    },

    customFieldValidations() {
      let validations = {};
      if (
        this.createCateObj.categoryFields &&
        this.createCateObj.categoryFields.length > 0
      ) {
        validations = {
          createCateObj: {
            categoryFields: {
              $each: {
                label: { required },
                type: { required },
                options: {},
              },
            },
          },
        };
      } else {
        validations = {
          createCateObj: {
            categoryFields: {
              $each: {
                label: {},
                type: {},
                options: {},
              },
            },
          },
        };
      }

      return validations;
    },
    issueTypeFieldValidations() {
      let validations = {};
      if (
        this.createIssueTypeObj.IssueTypeFields &&
        this.createIssueTypeObj.IssueTypeFields.length > 0
      ) {
        validations = {
          createIssueTypeObj: {
            IssueTypeFields: {
              $each: {
                label: { required },
                options: {},
              },
            },
          },
        };
      } else {
        validations = {
          createIssueTypeObj: {
            IssueTypeFields: {
              $each: {
                label: {},
                type: {},
                options: {},
              },
            },
          },
        };
      }

      return validations;
    },
    showToast(message, type) {
      this.toastMessage = message;
      this.toastType = type;
      this.toastFlag++;
    },
  },
  mounted() {
    // console.log(this.$route.params.viewFlag); // outputs 'yay'
    this.currentUserDetails = JSON.parse(
      localStorage.getItem("currentUserDetails")
    );
    if (this.$route.params.editFlag) {
      if (this.cateObject != "") {
        this.categoryName = this.cateObject.categoryTemplate.name;
        this.createCateObj.categoryFields =
          this.cateObject.categoryTemplate.categoryFiled.fields;
        this.createIssueTypeObj.IssueTypeFields =
          this.cateObject.categoryTemplate.issuesCategory.fields;
      }
    }
  },

  validations: function () {
    return {
      ...this.customFieldValidations(),
      ...this.issueTypeFieldValidations(),
      categoryName: { required },
    };
  },
  // props: {
  //   cateObject: {},
  //   showToast: { type: Function },
  // },
  computed: {},
  components: {
    loader,
  },
};
</script>

<style lang="scss" scoped>
.main-wrapper {
  display: grid;
  column-gap: 20px;
  grid-template-rows: 1fr;
}
.cate-field {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-column-gap: 20px;
  row-gap: 20px;
  margin-bottom: 28px;
}
.custom-fields {
  display: grid;
  grid-template-columns: repeat(6, auto);
  grid-gap: 10px;
  padding-left: 30px;
  padding-right: 30px;
}
.check-box {
  display: grid;
  grid-template-columns: 1fr 1fr;
  align-items: center;
  padding-left: 10px;
}
.option-field {
  grid-column-start: 3;
  grid-column-end: 5;
}
.pt-15 {
  padding-top: 15px;
}
.custom-btn {
  align-items: center;
  text-align: left;
  width: 150px;
  margin: 5px 20px 20px 0;
  padding-left: 10px;
  display: flex;
  height: 10px;
}
.custom-btn span {
  font-weight: bold;
  font-size: 16px;
  padding-left: 10px;
}

.button-row {
  display: grid;
  grid-gap: 10px;
  float: right;
  align-items: right;
  margin-right: 20px;
  margin-bottom: 20px;
}
</style>
