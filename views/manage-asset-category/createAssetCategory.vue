<template>
  <div class="mt-20">
    <div style="margin-bottom: 40px">
      <span class="head-title">Create Asset Category</span>
    </div>
    <div class="main-wrapper">
      <div class="cate-field">
        <SInput
          label="Asset Category Name*"
          v-model="categoryName"
          :error="getFormErrorMessage($v.categoryName)"
        />
      </div>
      <!-- <div class="full-row subtitle mt-8 mb-8">Description</div> -->
      <STextarea
        :columns="70"
        label="Asset Category description here"
        v-model="messageBox"
        :error="getFormErrorMessage($v.messageBox)"
      />
    </div>
    <div class="mt-20">
      <SAccordion
        title="Category Templates"
        expandIcon="arrows"
        padding="48"
        :open="false"
        :shadow="true"
      >
        <div class="custom-btn-main">
          <div class="custom-btn">
            <span>Additional Details</span>
            <SButton
              :dropdown="false"
              :title="'+'"
              @clicked-item="addCategoryField"
            ></SButton>
          </div>

          <div
            class="custom-fields pt-8 mb-8"
            v-for="(item, index) in createCateObj.categoryFields"
            :key="index"
          >
            <div class="label-field">
              <SInput
                label="Label"
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
                label="seprate with comma"
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
            <div class="pt-15">
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
        title="Categories & Types"
        expandIcon="arrows"
        padding="48"
        :open="false"
        :shadow="true"
      >
        <div class="custom-btn-main">
          <div class="custom-btn">
            <span>Additional Details</span>
            <SButton
              :dropdown="false"
              :title="'+'"
              @clicked-item="addissueTypesField"
            ></SButton>
          </div>

          <div
            class="custom-fields pt-8 mb-8"
            v-for="(item, index) in createIssueTypeObj.IssueTypeFields"
            :key="index"
          >
            <div class="label-field">
              <SInput
                label="Label"
                v-model="item.label"
                :error="
                  getFormErrorMessage(
                    $v.createIssueTypeObj.IssueTypeFields.$each[index].label
                  )
                "
              />
            </div>
            <!-- <div class="type_field">
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
            </div> -->
            <div class="option-field">
              <SInput
                label="seprate with comma"
                v-model="item.options"
                :error="
                  getFormErrorMessage(
                    $v.createIssueTypeObj.IssueTypeFields.$each[index].options
                  )
                "
                :ref="`options${index}`"
              />
            </div>
            <!-- <div class="check-box">
              <span>Mandatory</span>
              <div class="check-box">
                <SCheckBox
                  type="checkbox"
                  v-model="item.mandatory"
                  @input="mandatoryYesHandler(item.mandatory, index)"
                />
              </div>
            </div> -->
            <div class="pt-15">
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
          :dropdown="false"
          :title="'Save'"
          @clicked-item="saveAddCategory"
        ></SButton>
      </div>
      <div class="item1">
        <!-- <SButton
          :dropdown="false"
          :title="'Update'"
          @clicked-item="updateAddCategory"
        ></SButton> -->
      </div>
    </div>
  </div>
</template>

<script>
import { required, maxLength } from "vuelidate/lib/validators";
import { Functions } from "@/shared/Functions";

export default {
  name: "createCategory",

  data() {
    return {
      currentUserDetails: "",
      categoryName: "",
      messageBox: "",
      createCateObj: {
        fieldType: [
          { name: "Text", value: "text" },
          { name: "Select", value: "select" },
        ],
        categoryFields: [],
      },
      createIssueTypeObj: {
        fieldType: [
          { name: "Text", value: "text" },
          { name: "Select", value: "select" },
        ],
        IssueTypeFields: [],
      },
      // createIssueTypeObj.IssueTypeFields
      updateComp: 0,
    };
  },
  methods: {
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
        // type: "",
        position: "",
        // mandatory: false,
        options: "",
      });
    },
    // createIssueTypeObj.IssueTypeFields
    saveAddCategory() {
      //  console.log("addCategory", this.createCateObj);
      this.$v.$touch();
      if (this.$v.$invalid) {
        this.$emit("showToast", "Please fill all required fields", "warning");
        return;
      } else {
        let valdiation = this.customOptionValdiationCheck();
        if (!valdiation) {
        
          // this.$emit("addCategory", this.createCateObj);
          var value = this.createCateObj;
          this.loaderFlag = true;
          let addCategory = {
            category: {
              tenantUUID: this.currentUserDetails.profile.organizationId,
              fieldTemplate: {
                type: value.categoryName + " field template",
                tenantUUID: this.currentUserDetails.profile.organizationId,
                fields: [],
              },
            },
          };

          let fields = [];
          for (let index = 0; index < value.categoryFields.length; index++) {
            let addCategoryField = value.categoryFields[index];
            let field = {};
            field.label = addCategoryField.label;
            field.type = addCategoryField.type;
            field.fieldPosition = addCategoryField.position;
            field.mandatory = addCategoryField.mandatory;
            field.options = addCategoryField.options;
            fields.push(field);
          }
          addCategory.category.fieldTemplate.fields = fields;

          var valIssueTypeFields = this.createIssueTypeCateObj;
          console.log("addCategory", addCategory,valIssueTypeFields);
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
          this.$emit("updateCategory", this.createCateObj);
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
            this.$emit(
              "showToast",
              "Please fill options field in Additional Field #" + count,
              "error"
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
                type: { required },
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
  },
  mounted() {
    this.currentUserDetails = JSON.parse(
      localStorage.getItem("currentUserDetails")
    );
    // if (this.cateObject != "") {
    //   this.createCateObj.categoryName = this.cateObject.name;
    //   this.createCateObj.messageBox = this.cateObject.description;
    //   this.createCateObj.categoryFields = this.cateObject.fieldTemplate.fields;
    // }
  },

  validations: function () {
    return {
      ...this.customFieldValidations(),
      ...this.issueTypeFieldValidations(),
      categoryName: { required },
      messageBox: { maxLength: maxLength(500) },
    };
  },
  // props: {
  //   cateObject: {},
  //   showToast: { type: Function },
  // },
  computed: {},
  components: {},
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
