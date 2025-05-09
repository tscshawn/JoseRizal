<template>
    <div class="pageContent">
        <div class="article-card">
            <div class="card-content">
                <h3 class="title">Add Article</h3>
                <p class="subtitle">Article name</p>
                <input type="text" placeholder="Enter article name" class="input-field" v-model="newArticle.title" />
                <p class="subtitle">Description</p>
                <textarea placeholder="Enter article description in markdown" class="textarea-field" v-model="newArticle.content_html"></textarea>
                <p class="subtitle">Related city</p>
                <select class="input-field" v-model="newArticle.city_id">
                    <option value="" disabled>Select a city</option>
                    <option v-for="city in cities" :key="city.id" :value="city.id">{{ city.name }}</option>
                </select>
                <button class="action-button" @click="addArticle">
                    Add Article
                </button>
                <p v-if="message" :class="['message', messageType]">{{ message }}</p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            newArticle: {
                title: "",
                content_html: "",
                city_id: null,
            },
            cities: [],
            message: "",
            messageType: "",
        };
    },
    mounted() {
        this.fetchCities();
    },
    methods: {
        getCookie(name) {
            const nameEQ = name + '=';
            const ca = document.cookie.split(';');
            for (let i = 0; i < ca.length; i++) {
                let c = ca[i];
                while (c.charAt(0) === ' ') c = c.substring(1, c.length);
                if (c.indexOf(nameEQ) === 0) return c.substring(nameEQ.length, c.length);
            }
            return null;
        },
        async fetchCities() {
            const token = this.getCookie("access_token");
            try {
                const response = await fetch("http://127.0.0.1:8000/cities", {
                    method: "GET",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": "Bearer " + token,
                    },
                });
                if (response.ok) {
                    const data = await response.json();
                    console.log("Cities fetched successfully:", data);
                    this.cities = data;
                }
            } catch (error) {
                console.error("Error fetching cities:", error);
            }
        },
        async addArticle() {
            const token = this.getCookie("access_token");

            const data = {
                title: this.newArticle.title,
                content_html: this.newArticle.content_html,
                city_id: this.newArticle.city_id,
            };
            if (!data.title.trim()) {
                this.showMessage("Article title is required", "error");
                return;
            }
            if (!data.content_html.trim()) {
                this.showMessage("Article description is required", "error");
                return;
            }
            try {
                const response = await fetch("http://127.0.0.1:8000/articles", {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": "Bearer " + token,
                    },
                    body: JSON.stringify(data),
                });
                if (response.ok) {
                    const responseData = await response.json();
                    console.log("Article added successfully:", responseData);
                    this.showMessage("Article added successfully", "success");
                    this.newArticle = { title: "", content_html: "", city_id: null };
                } else {
                    const errorData = await response.json();
                    console.error("Error adding article:", errorData);
                    this.showMessage(errorData.message || "Failed to add article", "error");
                }
            } catch (error) {
                console.error("Error adding article:", error);
                this.showMessage("Failed to add article", "error");
            }
        },
        showMessage(message, type) {
            this.message = message;
            this.messageType = type;
            setTimeout(() => {
                this.message = "";
                this.messageType = "";
            }, 3000);
        },
    },

}
</script>

<style scoped>
.pageContent {
  display: flex;
  padding: 16px;
  background-color: #f5f5f5;
}

.article-card {
  width: 100%;
  max-width: 100%;
  background-color: white;
  border-radius: 12px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  transition: transform 0.3s, box-shadow 0.3s;
}

.card-content {
  padding: 16px;
}

.input-field {
  width: 100%;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 14px;
  margin-bottom: 12px;
  box-sizing: border-box;
}

.input-field:focus {
  border-color: #666666;
  box-shadow: 0 0 0 3px rgba(140, 140, 140, 0.200);
  outline: none;
}

.input-field::placeholder {
  color: #999999;
  opacity: 0.5;
}

.action-button {
  width: 100%;
  padding: 12px;
  margin-top: 16px;
  background-color: #999999;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.3s;
  font-size: 16px;
}

.action-button:hover {
  background-color: #666666;
}

.message {
  margin-top: 16px;
  padding: 12px;
  border-radius: 6px;
  font-size: 14px;
  text-align: center;
}

.success {
  background-color: #c6f6d5;
  color: #276749;
}

.error {
  background-color: #fed7d7;
  color: #c53030;
}

.textarea-field {
  width: 100%;
  height: 120px;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 14px;
  margin-bottom: 12px;
  box-sizing: border-box;
  resize: vertical;
}

.textarea-field:focus {
  border-color: #666666;
  box-shadow: 0 0 0 3px rgba(140, 140, 140, 0.200);
  outline: none;
}

.textarea-field::placeholder {
  color: #999999;
  opacity: 0.5;
}

@media screen and (min-width: 768px) {
  .page-content {
    padding: 32px;
  }

  .article-card {
    max-width: 480px;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  }

  .card-content {
    padding: 24px;
  }

  .action-button {
    transition: transform 0.2s, background-color 0.3s;
  }

  .action-button:hover {
    transform: translateY(-2px);
  }

  .article-card:hover {
    transform: translateY(-4px);
    box-shadow: 0 12px 20px rgba(0, 0, 0, 0.15);
  }
}
</style>